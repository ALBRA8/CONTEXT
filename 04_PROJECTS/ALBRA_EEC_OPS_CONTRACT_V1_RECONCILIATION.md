# ALBRA — Reconciliación del contrato EEC Ops V1

- Estado: PROPOSED / IMPLEMENTATION-PARTIAL / NOT INDEPENDENTLY TESTED
- Fecha de revisión: 2026-11-12
- Repositorios fuente consultados: `ALBRA8/INSTALADOR` y `ALBRA8/PANEL-DE-CONTROL`
- Alcance: contrato de operaciones PANEL ↔ INSTALADOR. No sustituye el contrato de descriptor de producto/despliegue.

## 1. Resumen ejecutivo

El INSTALADOR define el sobre de respuesta EEC V1 con los datos operativos anidados en `result`. El cliente del PANEL, su persistencia y la consulta de agentes estaban leyendo algunos campos en la raíz. Esa discrepancia podía hacer que una operación aceptada pareciera no tener `runId` o estado.

Se detectaron además dos operaciones presentes en el tipo/contrato del INSTALADOR que no estaban expuestas por el mapa HTTP del PANEL: `STATUS` y `RESTORE`.

Se publicaron cambios correctivos en `ALBRA8/PANEL-DE-CONTROL` (todos en `main`):

| Commit | Alcance |
|---|---|
| `9a23fce138a6f690cb700040852f599192b72fa9` | Cliente EEC: respuesta anidada, validación del sobre y correlación request/response. |
| `71651db7a06c38919b6e1ebaa9f2d49940f6c73c` | Persistencia: lee `result.runId` y guarda el objeto `result` completo. |
| `0bc29a211d390106fb2d7b85e3d7cd37465c28b2` | Vista de agentes: lee estado desde `response.result.state`. |
| `e4b7069059459d9642502db61f313a177ca2fb42` | Ruta de operaciones: expone `STATUS` y `RESTORE`. |
| `f39cb151fadde0aed341bd63b38b7aef4bed5a2e` | Tests de contrato: casos de respuesta anidada, correlación y rechazo malformado. |
| `48bced164e64e85b1fe9f1f929384784e86e1689` | Exporta el validador del sobre para pruebas. |

**Límite de evidencia:** estos commits fueron publicados, pero en esta sesión no se ejecutaron Bun tests, typecheck, lint ni build. No clasificarlos como verificados por ejecución.

## 2. Sobre canónico de solicitud

Método y endpoint: `POST /api/ops`

Autenticación: `Authorization: Bearer <ops-token>`.

```json
{
  "contractVersion": "1.0.0",
  "requestId": "UUID",
  "operation": "INSTALL",
  "productId": "chismoso",
  "params": {},
  "issuedAt": "2026-11-12T12:00:00.000Z",
  "expiresAt": "2026-11-12T12:04:00.000Z",
  "nonce": "valor-aleatorio-unico"
}
```

Reglas observadas en el INSTALADOR:
- Versión V1 literal: `1.0.0`.
- `requestId`: identificador de correlación de la solicitud.
- `nonce`: valor anti-replay distinto del requestId.
- Ventana máxima declarada: cinco minutos.
- `productId`: identifica el producto del catálogo.
- `params`: parámetros propios de la operación, validados por el INSTALADOR.

## 3. Sobre canónico de respuesta

Todos los datos propios de la operación viven bajo `result`, nunca en la raíz.

Aceptación de una operación asíncrona:

```json
{
  "contractVersion": "1.0.0",
  "requestId": "UUID-correlacionado",
  "accepted": true,
  "operation": "INSTALL",
  "productId": "chismoso",
  "result": { "runId": "run-id-real" }
}
```

Respuesta de estado:

```json
{
  "contractVersion": "1.0.0",
  "requestId": "UUID-correlacionado",
  "accepted": true,
  "operation": "STATUS",
  "productId": "chismoso",
  "result": {
    "state": {
      "state": "RUNNING",
      "health": "HEALTHY",
      "version": "1.0.0"
    }
  }
}
```

Rechazo de negocio:

```json
{
  "contractVersion": "1.0.0",
  "requestId": "UUID-correlacionado",
  "accepted": false,
  "operation": "INSTALL",
  "productId": "chismoso",
  "error": { "code": "PRODUCT_NOT_INSTALLED", "message": "..." }
}
```

Interpretación:
- `accepted=true` significa que la solicitud fue aceptada por el ejecutor; no significa que una instalación asíncrona haya terminado.
- `result.runId` identifica la ejecución asíncrona.
- El estado final debe obtenerse consultando el run y/o solicitando `STATUS`.
- `accepted=false` requiere `error.code` y `error.message`.
- La respuesta debe conservar y correlacionar `contractVersion`, `requestId`, `operation` y `productId`.

## 4. Operaciones V1

El enum consultado en el INSTALADOR declara:

`INSTALL, START, STOP, RESTART, HEALTH, UPDATE, ROLLBACK, BACKUP, RESTORE, UNINSTALL, DIAGNOSTICS, STATUS`.

El PANEL debe exponer las doce operaciones y respetar parámetros por operación. El mapa HTTP corregido incorpora `STATUS` y `RESTORE`; RESTORE requiere `backupId`.

## 5. Separación de contratos

Este documento cubre operaciones de ciclo de vida. No debe confundirse con el descriptor del producto.

El descriptor de producto/deployment debe declarar, como mínimo:
- identidad estable (`productId`), nombre y versión;
- origen de artefacto y política de confianza;
- modalidad de instalación y manifiesto Compose cuando aplique;
- servicios, imágenes fijadas por digest y health checks;
- secretos generados y variables estáticas, sin incluir valores secretos;
- volúmenes/datos y política de backup/restore;
- requisitos de plataforma y dependencias.

El PANEL no debe inventar ni completar silenciosamente estos campos: debe presentar al INSTALADOR el producto declarado por su catálogo/descriptor y mostrar los rechazos de validación.

## 6. Pendientes detectados para cierre

1. **Estado asíncrono en ManagedOperation:** la implementación observada fija `resolvedAt` cuando recibe cualquier respuesta, incluso `accepted=true` con `runId`. Eso puede marcar como resuelta una instalación que apenas fue aceptada. Debe distinguirse aceptación de finalización y actualizar el registro al consultar el run.
2. **Persistencia de anti-replay:** el INSTALADOR mantiene actualmente el registro de nonce en memoria por instancia. Definir almacenamiento persistente/compartido con TTL antes de escalar a múltiples instancias o reinicios.
3. **Validación temporal:** además de comprobar que `expiresAt` no haya pasado y que la ventana no exceda cinco minutos, revisar que `issuedAt` no esté indebidamente en el futuro y que la solicitud no pueda permanecer válida por una ventana desplazada.
4. **HTTP y errores:** documentar por separado errores de transporte/autenticación HTTP (401/4xx/5xx) y rechazos de negocio con `accepted=false`.
5. **Idempotencia y concurrencia:** definir qué sucede si el PANEL reintenta por timeout una solicitud cuya ejecución ya comenzó; `requestId` debe poder servir para deduplicación persistente, no solo auditoría.
6. **Seguimiento de run:** fijar esquema estable del DTO de `GET /api/runs/{id}`, estados terminales, progreso y política de retención.
7. **Pruebas compartidas:** idealmente extraer tipos/schema a paquete versionado compartido o mantener fixtures de contrato idénticos en ambos repositorios para evitar divergencias.
8. **Seguridad de token:** documentar provisión inicial, rotación, revocación y entrega segura al PANEL. No persistir el token en logs ni en ManagedOperation.

## 7. Criterios de aceptación

- [ ] Tests de contrato del PANEL pasan.
- [ ] Tests del INSTALADOR pasan.
- [ ] Typecheck, lint y build del PANEL pasan.
- [ ] Typecheck, lint y tests del INSTALADOR pasan.
- [ ] INSTALL devuelve `accepted=true` y `result.runId`; PANEL persiste el runId.
- [ ] Un run en progreso no se registra como finalizado.
- [ ] STATUS muestra estado y salud reales desde `result.state`.
- [ ] HEALTH muestra comprobaciones desde `result.health`.
- [ ] RESTORE transmite `params.backupId` y el INSTALADOR valida el respaldo.
- [ ] Respuesta con requestId/productId/operation/version distintos se rechaza.
- [ ] Rechazos de negocio y fallos de transporte se distinguen.
- [ ] Nonce repetido se rechaza incluso tras reinicio y entre instancias, cuando se cierre el requisito de persistencia.

## 8. Estado de decisión

La forma anidada `result` es la forma canónica **porque es la que ya implementa y valida el contrato del INSTALADOR V1**. Este documento registra la reconciliación técnica observada; no declara aprobadas nuevas políticas de seguridad ni una versión EEC V2.

La aprobación normativa final del propietario sigue pendiente. Hasta entonces, tratar el documento como propuesta de consolidación y no como modificación del ADN de ALBRA.
