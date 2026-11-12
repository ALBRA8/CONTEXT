# ALBRA — Auditoría de compatibilidad EEC Ops V1 contra HEADs actuales

- Estado: VERIFIED DOCUMENTATION / STATIC CODE REVIEW / NOT RUNTIME-TESTED
- Fecha: 2026-11-12
- Alcance: lectura de los HEAD públicos de INSTALADOR y PANEL-DE-CONTROL; sin escrituras en repositorios de producto.
- Repositorios autorizados para escritura en este flujo: solo `ALBRA8/CONTEXT`.

## 1. HEADs y fuentes examinadas

| Repositorio | Rama | HEAD consultado |
|---|---|---|
| `ALBRA8/INSTALADOR` | `main` | `dcf33e734ceb94e13437c96ea504e36c9a67e7a3` |
| `ALBRA8/PANEL-DE-CONTROL` | `main` | `48bced164e64e85b1fe9f1f929384784e86e1689` |

Archivos principales consultados:

**INSTALADOR**
- `src/app/api/ops/route.ts` — blob `9cb96a971a45964544c9e7f6f0f4a55e86c29343`
- `src/lib/albra/core/ops.ts` — blob `4b05ff645706736ea2d4a76484a85c73771568f9`
- `src/lib/albra/contracts/eec.ts` — blob `3bb818293ff13479fb8e90f6d2c53f361ba94ae7`
- `src/app/api/runs/route.ts` — blob `4ca7b591221763004bf25fd6ed9ed3840f3adae7`
- `src/app/api/runs/[id]/route.ts` — blob `cb344556b35e9e5081fa4abebe4094c786b5d417`
- `src/lib/albra/core/runs.ts` — blob `d0c8a75922c69bc8f7acafd524f785125b41c9fc`
- `src/lib/albra/api-serialize.ts` — blob `3230b6baf83f7973581526b0829dcffebee741c0`
- `prisma/schema.prisma` — blob `1d9c8e7993c6943d91742d505036295d9f50745d`
- `docs/EEC-CONTRACT.md` — blob `50dce81b246d86687f5232a8e3895f48f7002db3`

**PANEL**
- `src/lib/albra-bridge/ops-client.ts` — blob `ffb5bb0e2e8ead1b7004a69954483f7156aa0ab9`
- `src/lib/albra-bridge/records.ts` — blob `9c7c82395f91f6d7c1537d27367c602d737b741f`
- `src/app/api/v1/managed/agents/[productId]/[op]/route.ts` — blob `195d9f2fe2df967db8cf494dc828c1e1e3a32aac`
- `src/app/api/v1/managed/runs/[runId]/route.ts` — blob `29be6c6259e6d362fe1f6ea66c191b0be642e9e9`
- `prisma/schema.prisma` — blob `5ead8965fd3fbd55435f3e361da83b55c9cad78e`

## 2. Hallazgo: el endpoint real de seguimiento existe

El INSTALADOR implementa:

| Método y ruta | Respuesta observada |
|---|---|
| `GET /api/runs?productId={id}&limit={n}` | `{ runs: LifecycleRunDTO[] }`; límite por defecto 20, máximo 200 |
| `GET /api/runs/{id}` | Un `LifecycleRunDTO` directamente; 404 `RUN_NOT_FOUND` si no existe |

El DTO real no es el envelope EEC propuesto en el addendum anterior. Su forma observada es:

```ts
interface LifecycleRunDTO {
  id: string;
  productId: string | null;
  operation: string;
  status: "RUNNING" | "SUCCESS" | "WARNING" | "FAILURE" | "ROLLED_BACK";
  steps: Array<{
    step: string;
    label: string;
    status: "PENDING" | "RUNNING" | "SUCCESS" | "WARNING" | "FAILURE" | "SKIPPED";
    detail?: string;
    at?: string;
    durationMs?: number;
    units?: unknown[];
  }>;
  error: string | null;
  startedAt: string;
  finishedAt: string | null;
}
```

El serializador construye `id`, no `runId`; `startedAt` y `finishedAt` son ISO; los pasos contienen el progreso observable. La ruta individual devuelve el DTO sin wrapper `result.run`.

**Corrección al diseño anterior:** el PANEL debe consumir el DTO real existente y mapearlo a su modelo interno. No debe exigir el envelope ilustrativo `{ result: { run: ... } }` del documento de ciclo de vida, salvo que se apruebe una nueva versión del endpoint. El identificador de URL se obtiene de `result.runId` recibido al ejecutar la operación; en el DTO de seguimiento aparece como `id`.

## 3. Estados reales y mapping seguro

El modelo `LifecycleRun` del INSTALADOR persiste:
- `RUNNING` con `finishedAt = null` mientras se ejecuta.
- `SUCCESS`, `WARNING`, `FAILURE` o `ROLLED_BACK` al cerrar el run.
- `finish()` fija `finishedAt` al cerrar.
- El progreso por pasos utiliza `PENDING`, `RUNNING`, `SUCCESS`, `WARNING`, `FAILURE`, `SKIPPED`.

Mapping sugerido para el PANEL:

| INSTALADOR | PANEL | Regla |
|---|---|---|
| RUNNING | RUNNING | No asignar finalización |
| SUCCESS | SUCCEEDED | Terminal exitoso |
| WARNING | COMPLETED_WITH_WARNINGS (o terminal no-success separado) | No degradar automáticamente a SUCCEEDED |
| FAILURE | FAILED | Terminal fallido |
| ROLLED_BACK | FAILED + outcome ROLLED_BACK (o estado propio) | No confundir rollback compensatorio con éxito del objetivo original |

El contrato actual no incluye `CANCELLED` como estado de LifecycleRun. No exponerlo como capacidad real hasta implementar y probar cancelación. El PANEL debería conservar el estado remoto original además del estado normalizado para no perder semántica.

## 4. Hallazgos concretos del PANEL

### P1 — resolvedAt se asigna demasiado pronto

En `recordManagedOp()`, `resolvedAt: response ? new Date() : null` asigna fecha cuando cualquier respuesta EEC existe, incluso `accepted: true` con `result.runId`. Esa respuesta confirma aceptación, no finalización.

El esquema `ManagedOperation` tiene `accepted`, `runId`, `resultJson`, `errorJson`, `durationMs`, `createdAt` y `resolvedAt`, pero no tiene `acceptedAt`, `finishedAt` ni un campo de estado de ejecución separado.

El endpoint proxy `GET /api/v1/managed/runs/[runId]` consulta el INSTALADOR y devuelve el run, pero el código inspeccionado no actualiza el registro `ManagedOperation` al recibir el estado terminal. Así, la persistencia del PANEL no refleja el ciclo de vida real.

**Corrección requerida:** separar `acceptedAt` de `finishedAt/resolvedAt`; dejar resolución nula al aceptar un run; al consultar el DTO, actualizar el registro asociado al `runId` solo después de validar producto/operación y un estado terminal conocido. La asociación debe ser exacta, no un update ambiguo por producto.

### P1 — el registro es best-effort y puede perderse silenciosamente

`recordManagedOp()` captura cualquier error de base de datos y no lo propaga. El endpoint puede devolver la respuesta real aunque no haya persistido el historial. Esto puede ser una decisión deliberada de disponibilidad, pero impide tratar `ManagedOperation` como auditoría durable o fuente de verdad.

Definir explícitamente si el registro es:
- auditoría best-effort (y se muestra como tal), o
- registro operacional requerido, con estrategia transaccional/outbox y manejo observable del fallo de persistencia.

### P1 — no hay idempotencia de solicitud demostrada

El PANEL genera un `requestId` y `nonce` nuevos en cada llamada a `buildOpsRequest()`. Si una solicitud se ejecutó en INSTALADOR pero la respuesta se perdió, un nuevo intento crea otra intención con otro requestId. El INSTALADOR no consulta ni deduplica por requestId; el código observado solo mantiene un `nonceRegistry` en memoria por instancia.

Consecuencia: el contrato actual no garantiza deduplicación ante timeout/reintento. No afirmar exactly-once ni reintentar automáticamente operaciones destructivas o costosas sin una clave de idempotencia durable.

### P2 — duración registrada no es duración de ejecución

`durationMs` se calcula como `Date.now() - startedAt` al recibir la respuesta de POST. Para operaciones asíncronas mide latencia de aceptación/solicitud, no duración total del run. Renombrar a `requestDurationMs` o documentar esa semántica; la duración real puede derivarse de `finishedAt - startedAt` del run.

## 5. Hallazgos concretos del INSTALADOR

### P1 — nonce anti-replay solo en memoria por instancia

`nonceRegistry` es un `Map<string, number>` de proceso. Se limpia por expiración. Un reinicio borra el registro; múltiples instancias no comparten nonces. La documentación del propio INSTALADOR declara este pendiente.

### P1 — issuedAt futuro no está limitado

El schema valida que `expiresAt > issuedAt` y que la ventana no supere cinco minutos. El handler comprueba que `expiresAt` no haya pasado. No se observa validación de que `issuedAt` no esté demasiado en el futuro. Una solicitud con ambas fechas futuras puede superar esas dos condiciones. Definir tolerancia de reloj y validar `issuedAt <= now + allowedClockSkew`.

### P1 condicional — endpoints de runs sin autenticación local visible

Los handlers `GET /api/runs` y `GET /api/runs/[id]` llaman a `ensureBootstrap()` y consultan la base, pero no verifican Bearer token ni otra identidad en el propio handler. A diferencia de `POST /api/ops`, que sí valida `Authorization: Bearer`, las rutas de lectura no muestran auth local.

El proxy del PANEL sí exige `resolveRequester()` antes de consultar el run. No se verificó si un firewall, reverse proxy o red privada restringe el acceso directo al INSTALADOR. Por ello el hallazgo es **exposición potencial condicionada a la topología**, no afirmación de que el servicio esté públicamente expuesto. Si las rutas son alcanzables fuera de una red de confianza, añadir autenticación/autorización o restricción de red antes de exponerlas.

### P2 — async declarado vs comportamiento condicional de UNINSTALL

El catálogo EEC declara `UNINSTALL` asíncrono. Sin embargo, la rama contenedorizada de `executeOps()` elimina el deployment y devuelve `detail` sin `runId`; la rama de producto no contenedorizado devuelve el `runId` de `runUninstall()`. La semántica efectiva depende del tipo de despliegue.

El PANEL debe guiarse por la respuesta real: si existe `runId`, hacer polling; si no existe, tratarla como respuesta síncrona solo después de validar que esa rama es intencional y documentada. Alternativamente, normalizar UNINSTALL para que siempre sea asíncrono o siempre síncrono, con decisión explícita.

## 6. Política HTTP observada

El handler `POST /api/ops`:
- devuelve HTTP 401 para Bearer ausente/inválido;
- devuelve HTTP 400 si el body no es objeto JSON válido;
- devuelve HTTP 200 con `accepted:false` para rechazos de negocio generados por `executeOps()`;
- devuelve HTTP 200 con `accepted:true` cuando la solicitud se ejecuta/acepta correctamente.

Por tanto, la propuesta de separar HTTP de errores de negocio del addendum anterior sí coincide con el comportamiento observado en este endpoint. No se debe extrapolar esta política a todos los endpoints de INSTALADOR.

## 7. Seguridad del proxy PANEL → INSTALADOR

El proxy de lectura del PANEL:
- exige un requester mediante `resolveRequester(req)`;
- valida el formato del runId;
- llama a `fetchAlbraGet("/api/runs/{id}")`.

El helper `fetchAlbraGet()` no añade el Bearer de `ALBRA_OPS_TOKEN`. Esto funciona con el endpoint actual porque el GET de run no exige Bearer. Si se decide proteger los GET con el token de operaciones, el helper debe añadirlo y las rutas deben validar scopes; alternativamente, usar una credencial de lectura separada de mínimo privilegio. No reutilizar automáticamente un token de administración en rutas con permisos más amplios de los necesarios.

## 8. Matriz de compatibilidad actualizada

| Área | Estado observado | Dictamen |
|---|---|---|
| POST /api/ops | Bearer + schema EEC + envelope nested result | Implementado en HEAD consultado |
| HTTP para rechazo de negocio | HTTP 200 + accepted:false + error | Implementado en HEAD consultado |
| GET /api/runs | Lista DTOs reales | Implementado; auth local no visible |
| GET /api/runs/{id} | DTO individual o 404 RUN_NOT_FOUND | Implementado; auth local no visible |
| Estados de run | RUNNING/SUCCESS/WARNING/FAILURE/ROLLED_BACK | Implementado |
| Progreso por pasos/unidades | steps + units | Implementado |
| Proxy PANEL run | Requiere requester y reenvía DTO | Implementado |
| Persistencia final del run en PANEL | No actualiza ManagedOperation en ruta de polling inspeccionada | Gap |
| resolvedAt en aceptación async | Se asigna con cualquier response | Defecto |
| Idempotencia por requestId | No se observa dedupe en INSTALADOR; PANEL genera IDs nuevos por llamada | Gap P1 |
| Nonce durable | Map en memoria por instancia | Gap P1 |
| Validación de issuedAt futuro | No observada | Gap P1 |
| Docker/runtime real | No probado en esta auditoría | NOT VERIFIED |

## 9. Decisiones técnicas recomendadas para el próximo ciclo

1. No crear endpoint de run nuevo: usar `GET /api/runs/{id}` y su DTO actual.
2. Definir `ManagedOperation` como registro de intención/aceptación y enlazarlo al `LifecycleRun` remoto por `runId`.
3. Persistir `acceptedAt`, `finishedAt`, `remoteStatus`, `normalizedStatus`, `requestDurationMs` y `executionDurationMs`; migrar sin reinterpretar registros históricos.
4. Actualizar el registro desde el polling únicamente al validar el run; guardar la última observación y no borrar estados terminales.
5. Diseñar idempotencia durable por identidad + requestId + hash de payload; el mismo requestId/payload recupera el run existente, distinto payload produce conflicto.
6. Persistir anti-replay compartido y cerrar validación temporal con tolerancia de reloj.
7. Acordar protección de GET /api/runs según la topología real; no exponer información de pasos/logs sin auth o red confiable.
8. Resolver la discrepancia de UNINSTALL asíncrono/síncrono.
9. Implementar solo tras autorización formal de gobernanza; después correr suites, typecheck, lint, build y E2E en ambos repositorios.

## 10. Límites de esta auditoría

- Fue una inspección estática de archivos públicos de los HEAD indicados, no ejecución de código.
- No se ejecutaron tests, typecheck, lint, build, curl ni Docker.
- No se comprobó la configuración de firewall, reverse proxy, TLS, DNS ni exposición pública de INSTALADOR.
- Los cambios de producto anteriores continúan publicados; no se añadieron commits a PANEL ni INSTALADOR en esta sesión.
- Este dictamen no es aprobación normativa ni certificación de producción.
