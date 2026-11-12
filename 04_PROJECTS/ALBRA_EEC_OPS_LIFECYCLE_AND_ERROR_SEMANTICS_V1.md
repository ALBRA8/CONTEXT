# ALBRA — EEC Ops V1: ciclo de vida, seguimiento y semántica de errores

- Estado: PROPOSED / DESIGN-ONLY / NOT IMPLEMENTED OR TESTED
- Fecha: 2026-11-12
- Depende de: `ALBRA_EEC_OPS_CONTRACT_V1_RECONCILIATION.md`
- Alcance: especificación de cierre para seguimiento asíncrono, persistencia del PANEL, errores e idempotencia.
- Autoridad: documento técnico propuesto; no modifica el ADN ni equivale a aprobación normativa del propietario.

## 1. Decisión central: aceptación no es finalización

Una respuesta EEC con `accepted: true` solo confirma que el INSTALADOR aceptó la solicitud. No demuestra que la acción haya terminado ni que haya tenido éxito.

Por tanto:
- `acceptedAt` registra cuándo el PANEL recibió la aceptación.
- `finishedAt` registra cuándo se observó un estado terminal fiable.
- `resolvedAt` no debe asignarse al recibir una aceptación asíncrona.
- `runId` identifica la ejecución creada por el INSTALADOR.
- `requestId` identifica la solicitud lógica y sirve para correlación/idempotencia.
- `nonce` es un mecanismo anti-replay de la solicitud firmada/autenticada; no sustituye a requestId.
- Una respuesta HTTP exitosa o `accepted: true` no debe convertirse automáticamente en `SUCCEEDED`.

## 2. Máquina de estados propuesta para ManagedOperation

Estados persistidos:

| Estado | Significado | Terminal |
|---|---|---:|
| `REQUESTED` | El PANEL creó la operación y está por enviarla o la está enviando. | No |
| `ACCEPTED` | El INSTALADOR confirmó `accepted: true`; puede existir runId. | No |
| `RUNNING` | El seguimiento informa ejecución activa. | No |
| `SUCCEEDED` | El run terminó correctamente y el resultado final fue confirmado. | Sí |
| `FAILED` | El run terminó con error confirmado. | Sí |
| `CANCELLED` | El INSTALADOR confirmó cancelación efectiva. | Sí |

Reglas:
1. `REQUESTED → ACCEPTED` solo tras validar el sobre y correlación.
2. `ACCEPTED → RUNNING` cuando el endpoint de seguimiento informa ejecución activa. Si el run ya terminó al primer sondeo, puede pasar directamente a un estado terminal.
3. `RUNNING → SUCCEEDED|FAILED|CANCELLED` solo con evidencia terminal del INSTALADOR.
4. Un timeout de red, error 5xx o fallo de lectura del estado NO significa `FAILED`; la operación queda en el último estado conocido y se registra un error de observación.
5. Un rechazo de negocio antes de crear un run se registra como fallo de solicitud/rechazo, sin inventar un runId. Para mantener la máquina uniforme, el estado persistido puede pasar a `FAILED` con `failureStage: "REJECTED"`.
6. No se permiten transiciones desde un estado terminal a otro estado terminal distinto. Correcciones administrativas deben quedar auditadas y no sobrescribir silenciosamente el historial.
7. Si el INSTALADOR devuelve un estado desconocido o un DTO inválido, el PANEL conserva el último estado conocido y registra `UNKNOWN_REMOTE_STATE`; nunca debe asumir éxito.

### Campos recomendados

```ts
type ManagedOperationStatus =
  | "REQUESTED"
  | "ACCEPTED"
  | "RUNNING"
  | "SUCCEEDED"
  | "FAILED"
  | "CANCELLED";

interface ManagedOperation {
  id: string;
  requestId: string;
  productId: string;
  operation: string;
  status: ManagedOperationStatus;

  runId: string | null;
  acceptedAt: string | null;
  startedAt: string | null;
  finishedAt: string | null;

  // Alias de compatibilidad opcional; solo se asigna al terminar.
  resolvedAt: string | null;

  progress: number | null; // 0..100, null si no está disponible
  phase: string | null;
  result: Record<string, unknown> | null;

  error: {
    code: string;
    message: string;
    retryable: boolean | null;
    stage: "REQUEST" | "REJECTED" | "EXECUTION" | "OBSERVATION";
  } | null;

  createdAt: string;
  updatedAt: string;
}
```

No almacenar tokens, nonces en claro innecesariamente ni secretos dentro de `result`, `error.message` o logs.

## 3. Seguimiento de ejecución (run)

El contrato previo menciona `GET /api/runs/{id}`, pero la ruta y su DTO no quedan certificados por la evidencia documental disponible. La siguiente forma es una propuesta normativa para acordar, no una afirmación de que el endpoint ya exista:

```json
{
  "contractVersion": "1.0.0",
  "requestId": "UUID-de-consulta",
  "accepted": true,
  "result": {
    "run": {
      "runId": "run-id",
      "requestId": "UUID-de-la-operacion-original",
      "productId": "chismoso",
      "operation": "INSTALL",
      "status": "RUNNING",
      "phase": "PULLING_IMAGES",
      "progress": 45,
      "createdAt": "2026-11-12T12:00:00.000Z",
      "startedAt": "2026-11-12T12:00:02.000Z",
      "finishedAt": null,
      "updatedAt": "2026-11-12T12:00:20.000Z",
      "result": null,
      "error": null
    }
  }
}
```

Para un run terminal:
- `status` debe ser exactamente uno de `SUCCEEDED`, `FAILED` o `CANCELLED`.
- `finishedAt` es obligatorio y no nulo.
- `progress` puede ser 100 en SUCCEEDED; no exigir 100 en FAILED/CANCELLED.
- `error` es obligatorio para FAILED; opcional para CANCELLED.
- `result` contiene salida no secreta y estable.
- Un run no terminal debe tener `finishedAt: null`.

La operación `STATUS` describe el estado/health actual del producto instalado; no sustituye el seguimiento de una ejecución concreta. El seguimiento por runId responde si INSTALL/UPDATE/RESTORE/etc. terminó.

### Mapeo remoto → PANEL

| Estado remoto | Estado ManagedOperation |
|---|---|
| QUEUED / ACCEPTED / PENDING | ACCEPTED |
| RUNNING / IN_PROGRESS | RUNNING |
| SUCCEEDED / COMPLETED | SUCCEEDED |
| FAILED / ERROR | FAILED |
| CANCELLED / CANCELED | CANCELLED |
| desconocido / ausente | conservar último estado + error de observación |

La lista de estados remotos es una propuesta de normalización. Debe confirmarse contra el enum real del INSTALADOR antes de codificarla.

## 4. Semántica HTTP frente a errores de negocio

Separar dos capas:

### Errores de transporte/protocolo

Son respuestas HTTP no exitosas o fallos de conexión. El PANEL no debe tratarlas como un rechazo de negocio válido si no recibió un sobre EEC íntegro.

| HTTP sugerido | Uso | Acción del PANEL |
|---|---|---|
| 400 | JSON inválido / esquema de solicitud incorrecto | No reintentar sin corregir solicitud |
| 401 | Token ausente, inválido o expirado | Bloquear; revisar credencial |
| 403 | Identidad autenticada sin permiso | Bloquear; no reintentar automáticamente |
| 404 | Producto o run no encontrado | Registrar recurso inexistente; revisar sincronización |
| 409 | Conflicto de estado o idempotencia incompatible | Consultar operación/run existente |
| 422 | Parámetros válidos sintácticamente pero semánticamente inválidos | Corregir parámetros |
| 429 | Rate limit | Reintentar con backoff y Retry-After |
| 5xx | Fallo interno temporal/no clasificado | Resultado ambiguo; consultar por requestId antes de reenviar |
| sin respuesta / timeout | Resultado desconocido | No crear otra operación lógica; reconciliar por requestId |

Los códigos son recomendación de contrato; deben alinearse con las rutas y handlers reales del INSTALADOR.

### Rechazos de negocio EEC

Cuando el servidor procesa la solicitud y decide rechazarla, debe devolver un sobre EEC válido con `accepted: false` y `error.code` + `error.message`. El HTTP puede ser 200 si se adopta una respuesta de negocio dentro del protocolo, o 4xx si se decide mapear rechazo a HTTP. No mezclar ambas políticas sin una tabla normativa única.

**Propuesta preferida:** usar HTTP 200 para una respuesta EEC de negocio bien formada (aceptada o rechazada); reservar HTTP 4xx/5xx para fallos de transporte, autenticación, autorización, validación de protocolo y servidor. Si el propietario elige otra política, documentar el mapping completo y aplicarlo en ambos repositorios.

Códigos de error sugeridos, sujetos a inventario real:
- `INVALID_REQUEST`
- `UNSUPPORTED_CONTRACT_VERSION`
- `PRODUCT_NOT_FOUND`
- `PRODUCT_NOT_INSTALLED`
- `INVALID_OPERATION`
- `INVALID_PARAMS`
- `INVALID_STATE`
- `RUN_NOT_FOUND`
- `BACKUP_NOT_FOUND`
- `OPERATION_CONFLICT`
- `IDEMPOTENCY_CONFLICT`
- `NONCE_REPLAYED`
- `REQUEST_EXPIRED`
- `EXECUTION_FAILED`

El PANEL debe usar `error.code` para lógica; `error.message` es texto de presentación y nunca debe ser parseado para decidir comportamiento.

## 5. Idempotencia por requestId

El objetivo es evitar que un timeout del PANEL cree dos instalaciones/actualizaciones/restauraciones.

Requisitos propuestos:
1. El PANEL genera un `requestId` UUID antes del primer envío y lo persiste con la operación.
2. Todos los reintentos de la misma intención reutilizan exactamente el mismo requestId y el mismo payload lógico.
3. El INSTALADOR mantiene un registro durable y compartido por todas sus instancias, con índice único por identidad autenticada + requestId (o scope equivalente acordado).
4. Al recibir un requestId ya procesado:
   - mismo hash de payload y misma identidad: devolver la respuesta original/estado actual, incluyendo el mismo runId; no ejecutar otra vez;
   - distinto hash de payload: rechazar con `IDEMPOTENCY_CONFLICT` (HTTP 409 o sobre de rechazo según política elegida).
5. El registro debe persistir más que el máximo horizonte de reintento y retención de operaciones; definir TTL explícito. No borrar una clave mientras su run siga activo.
6. La creación del registro idempotente y el enqueue del run deben ser atómicos o usar patrón transactional outbox/inbox equivalente. Evitar ventana donde se registra requestId sin run o se crea run sin dedupe.
7. El PANEL no debe generar un requestId nuevo solo porque no recibió respuesta. Primero debe consultar/reconciliar por el requestId original.
8. Idempotencia de aceptación no equivale a exactly-once de efectos externos; documentar límites por operación y por proveedor.

## 6. Anti-replay y tiempo

Separar idempotencia de anti-replay:
- `requestId` deduplica una intención lógica y permite recuperar su resultado.
- `nonce` impide reutilizar una solicitud autenticada fuera de su contexto temporal.
- Un reintento legítimo debe poder recuperar la respuesta asociada al requestId sin volver a ejecutar el efecto, aunque el nonce original ya haya sido consumido. El mecanismo exacto (replay de respuesta idempotente antes de rechazar nonce, o nuevo sobre firmado ligado al mismo requestId) debe definirse explícitamente.
- El registro de nonce debe ser durable/compartido entre instancias, con TTL al menos hasta `expiresAt` más margen de reloj.
- Validar `issuedAt` contra tolerancia de clock skew, rechazar fechas futuras fuera de tolerancia, validar `expiresAt > issuedAt` y exigir que la ventana no exceda cinco minutos (si se mantiene ese límite).
- Registrar decisión y motivo sin escribir secretos ni credenciales.

## 7. Matriz de contrato pendiente de verificación cruzada

| Superficie | PANEL (documentado) | INSTALADOR (documentado) | Cierre requerido |
|---|---|---|---|
| Envío | POST /api/ops | POST /api/ops | Confirmar path, headers y DTO real en ambos HEAD |
| Aceptación | Lee envelope y result.runId | accepted + result | Fixture compartido de respuesta |
| STATUS | Expuesto por mapa de operaciones | Enum STATUS; result.state | Confirmar params y shape exacto |
| RESTORE | Transmite backupId | Enum RESTORE | Confirmar nombre/ubicación de param y validación |
| Run tracking | Persiste runId; resolución pendiente | Ruta GET /api/runs/{id} referida, DTO no certificado | Verificar endpoint, estados y retención |
| HEALTH | Debe leer result.health | Contrato documentado como result.health | Confirmar estructura exacta |
| Auth | Bearer ops-token | Bearer ops-token | Provisión, scopes, rotación y revocación |
| Errores | Valida error del sobre | accepted=false + error | Acordar mapping HTTP/business |
| Idempotencia | requestId generado/persistido | Dedupe durable no demostrado | Implementar almacenamiento compartido |
| Anti-replay | nonce enviado | nonce en memoria por instancia según revisión | Persistencia compartida + reglas temporales |

Esta matriz registra lo conocido en la revisión, no sustituye la inspección de los HEAD actuales. Las celdas marcadas como cierre requerido no deben darse por implementadas.

## 8. Pruebas de contrato mínimas

Fixtures compartidos PANEL/INSTALADOR:
- aceptación asíncrona con runId: persiste ACCEPTED, acceptedAt y runId; finishedAt/resolvedAt quedan null.
- primer polling RUNNING: cambia a RUNNING; no asigna finishedAt.
- polling SUCCEEDED/FAILED/CANCELLED: estado terminal, finishedAt y resolvedAt; no permitir mutación terminal posterior.
- timeout después de enviar: reintento conserva requestId y recupera el mismo runId; no duplica ejecución.
- mismo requestId con payload distinto: IDENTITY/IDEMPOTENCY CONFLICT.
- nonce repetido: rechazo, salvo flujo explícito de recuperación idempotente ya definido.
- 401/403/429/5xx y timeout: no se convierten en FAILED de ejecución.
- sobre accepted=false sin error, requestId incorrecto, operation/productId/version incorrectos: rechazo de protocolo.
- runId inexistente, DTO malformado y estado remoto desconocido: fallo cerrado de observación, sin marcar SUCCEEDED.
- RESTORE sin backupId y backupId inexistente: rechazo claro, sin run.
- logs y respuestas no contienen token, secretos, nonce sensible ni datos internos innecesarios.

## 9. Secuencia de cierre recomendada

1. Confirmar en los HEAD actuales del INSTALADOR los endpoints reales de operaciones y runs, DTOs, enums y códigos HTTP.
2. Confirmar en PANEL el modelo persistido y la ruta de polling actual.
3. Acordar formalmente la política HTTP/business y la recuperación idempotente con nonce.
4. Crear fixtures JSON/schema compartidos y tests de contrato en ambos repositorios.
5. Implementar el ciclo de vida y la persistencia durable/compartida solo después de autorización explícita para escribir los repositorios de producto.
6. Ejecutar tests, typecheck, lint y build en ambos; después realizar E2E sin Docker y finalmente pruebas con Docker real, backup/restore y reinicio multi-instancia.
7. No declarar producción lista hasta verificar despliegue, almacenamiento persistente, auth/rotación y recuperación tras fallo.

## 10. Estado final de este addendum

Este documento cierra una propuesta de diseño, no la implementación. El bug potencial de `resolvedAt` señalado en la reconciliación previa permanece abierto hasta inspeccionar y corregir el código bajo autorización. No se afirma que exista actualmente el endpoint de run propuesto, ni que idempotencia o anti-replay sean durables. Los cambios publicados en PANEL siguen sin tests/build independientes ejecutados en esta sesión.


## Addendum de evidencia — HEADs consultados el 2026-11-12

La auditoría estática posterior [ALBRA_EEC_CURRENT_HEAD_COMPATIBILITY_AUDIT_2026-11-12.md](../03_AUDITS/ALBRA_EEC_CURRENT_HEAD_COMPATIBILITY_AUDIT_2026-11-12.md) encontró que el INSTALADOR ya implementa `GET /api/runs/{id}` y devuelve un `LifecycleRunDTO` directo (`id`, `status`, `steps`, `error`, `startedAt`, `finishedAt`). Por tanto, el envelope `{ result: { run: ... } }` de la sección 3 de este documento queda como **ejemplo de diseño no adoptado**, no como contrato implementado. El PANEL debe consumir el DTO real y mapear sus estados conservando `remoteStatus`.

Estados reales del run: `RUNNING`, `SUCCESS`, `WARNING`, `FAILURE`, `ROLLED_BACK`; no hay `CANCELLED` demostrado. La auditoría también confirma el defecto de `resolvedAt` prematuro, la ausencia de actualización del ManagedOperation durante el polling inspeccionado, idempotencia por requestId no demostrada, nonce solo en memoria, falta de límite para issuedAt futuro y UNINSTALL con respuesta condicionalmente síncrona en despliegues contenedorizados.

La política HTTP observada en `POST /api/ops` sí usa HTTP 200 para rechazos de negocio (`accepted:false`) y 401 para autenticación inválida. Ver el informe de auditoría para rutas, blobs y límites de evidencia.
