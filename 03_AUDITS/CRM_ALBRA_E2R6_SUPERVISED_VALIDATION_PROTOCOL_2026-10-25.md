# CRM-ALBRA — E.2.R.6
# Protocolo de validación operativa supervisada
## Auditoría previa y diseño técnico · Cero despachos reales · Sin ejecución operativa

- **Fuente:** informe E.2.R.6 aportado por el propietario/Antigravity el 2026-10-25.
- **Dictamen fuente:** `READY FOR AUTHORIZATION REVIEW`.
- **Estado documental de este registro:** el protocolo queda archivado, pero **NO debe considerarse autorizado para ejecución**. Se añade una corrección crítica de seguridad a la sonda propuesta en la sección 6.
- **Alcance:** documentación en `ALBRA8/CONTEXT` únicamente. No certifica por sí sola el código local, los resultados de pruebas ni el runtime de CRM-ALBRA.

## 1. Resumen ejecutivo

El informe fuente declara que E.2.R.5 corrigió el bypass de `senderType` en `POST /send` mediante validación estricta de `agent` o `user`, validación de operador humano activo y aislamiento por organización. También declara 35/35 pruebas herméticas/regresión PASS, con transporte simulado y cero llamadas en los casos denegados.

Según el informe, los procesos `task-3480` (Next.js, puerto 3000) y `task-3638` (daemon, puerto 3002) no se reiniciaron; por ello, la versión corregida cargada en memoria sigue **NOT VERIFIED**. El despacho real por WhatsApp no está autorizado.

Estas afirmaciones se registran como **resultados reportados por la fuente**, no como una reejecución independiente de código o pruebas durante esta actualización documental.

## 2. Estado reportado del repositorio

| Componente | Ruta | Estado declarado por el informe |
|---|---|---|
| Validador de tipo de remitente | `src/whatsapp-daemon/dispatch-gate.mjs` | `STATIC VERIFIED` |
| Validador de operador | `src/whatsapp-daemon/dispatch-gate.mjs` | `STATIC VERIFIED` |
| Compuerta `POST /send` | `src/whatsapp-daemon/daemon.mjs` | `STATIC VERIFIED` |
| Barrera física de transporte | `src/whatsapp-daemon/daemon.mjs` | `STATIC VERIFIED` |
| Proxy Next.js | `src/app/api/whatsapp/daemon-proxy/route.ts` | Identidad `userId/orgId` inyectada desde JWT, según informe |
| Bandeja omnicanal | `src/lib/inbox.ts` | Propagación de identidad reportada |
| Suite E.2.R.5 | `tests/unit/e2r5-sendertype-bypass.test.mjs` | 13 pruebas herméticas reportadas |
| Daemon en memoria | `task-3638` | Versión corregida no confirmada; no reiniciado |
| Despacho real | `ALLOW_REAL_DISPATCH=false`, `isAutoReply=0` | Valores preservados según informe; no se revalidaron aquí |

## 3. Hallazgos de seguridad reportados

### 3.1 Validación fail-closed de `senderType`

El informe declara que los valores ausentes, nulos, no string y desconocidos se rechazan con HTTP 400; únicamente se admiten `agent` y `user`. La intención es eliminar la caída implícita de valores no reconocidos hacia `isAgent: false`.

### 3.2 Rama de operador humano

El informe declara que `validateDaemonUserDispatch` exige un identificador de operador, comprueba que el usuario exista y esté activo en SQLite y que su `organizationId` coincida con `linkedOrgId`. La identidad se propaga desde `requireAuth`/JWT a través del proxy y la bandeja.

### 3.3 Rama de agente y transporte

El informe declara que la rama `agent` mantiene validación de gate, ticket HMAC/TTL, hash de payload, destinatario, estado de propuesta, nonce y anti-replay; también declara que la llamada a `sock.sendMessage()` pasa por `sendWhatsAppMessage()`, que aplica la barrera física `assertTransportDispatchAllowed()`.

### 3.4 Automatizaciones sin clasificación

El informe señala que `src/lib/workflow-engine.ts:181` enviaba solicitudes sin `senderType`. Bajo el contrato descrito, el daemon debería rechazarlas por falta de clasificación y autorización explícita. La integración funcional de ese flujo queda pendiente de validación en runtime.

## 4. Pruebas herméticas propuestas y resultados previos reportados

El protocolo propone utilizar una base temporal independiente (`test-e2r6-isolated.db`), secretos de prueba separados y un socket simulado/spy. La matriz incluye:

1. `senderType` ausente o `null` → HTTP 400, cero invocaciones.
2. Tipo no string o cadena vacía → HTTP 400, cero invocaciones.
3. Valor desconocido → HTTP 400, cero invocaciones.
4. `agent` sin ticket → HTTP 423, cero invocaciones.
5. Ticket caducado/manipulado → rechazo, cero invocaciones.
6. Hash de payload no coincidente → HTTP 403, cero invocaciones.
7. Nonce reutilizado → rechazo, cero invocaciones.
8. `user` sin identidad → HTTP 403, cero invocaciones.
9. Operador inactivo o de otra organización → HTTP 403, cero invocaciones.
10. Operador activo en sandbox → una invocación al mock.
11. Agente con ticket de sandbox válido → una invocación al mock.
12. Barrera física con gate cerrado → excepción de bloqueo, cero invocaciones.

El informe anterior declara 13/13 para `e2r5-sendertype-bypass.test.mjs`, 9/9 para `e2r-double-lock.test.mjs` y 13/13 para `controlled-dispatch.test.mjs` (35/35 en total). **Estos resultados no se reejecutaron al registrar este documento.** El transporte simulado no equivale a validación del runtime o a un envío real.

## 5. Protección de SQLite/WAL propuesta

El informe prohíbe copiar únicamente `db/custom.db` mientras pueda depender de `custom.db-wal`, así como borrar, renombrar o truncar manualmente archivos WAL/SHM.

Propone una copia consistente mediante la API de backup de SQLite/better-sqlite3 y una comprobación `PRAGMA integrity_check` sobre la copia. La implementación exacta del comando propuesto debe validarse en un entorno aislado y con la versión real de la biblioteca antes de una ventana: no se debe asumir que el ejemplo textual es ejecutable sin adaptación. No ejecutar backup, checkpoint ni operaciones de escritura en la base operativa sin autorización expresa.

Las fechas, tamaños y marcas de tiempo de los archivos no son evidencia suficiente por sí solos de integridad. Cualquier copia de producción debe tener destino, permisos, método de verificación y procedimiento de restauración documentados antes de empezar.

## 6. Recarga y comprobación de runtime — CORRECCIÓN CRÍTICA

El informe fuente propone detener y volver a iniciar el daemon y Next.js en una ventana supervisada. También propone como sonda de versión una petición:

`POST http://localhost:3002/send`

con autorización interna y `senderType: "sonda_invalida"`, esperando HTTP 400.

### Esta sonda NO está aprobada y NO debe ejecutarse tal como está descrita.

**Motivo:** el propio historial de E.2.R.4/R.5 identifica que el daemon que aún está en memoria puede ser la versión anterior, en la que valores distintos de `agent` podían caer en la rama manual (`isAgent: false`). Precisamente porque la versión corregida no está confirmada, enviar un `senderType` inválido a ese proceso podría no ser rechazado por el nuevo validador y podría alcanzar el transporte. El resultado esperado de la versión nueva no demuestra que la versión antigua sea segura.

Por tanto:
- No enviar la sonda al puerto operativo 3002 antes de recargar y comprobar el código cargado mediante un mecanismo seguro.
- No usar un destinatario real ni un payload de envío para identificar la versión.
- No tratar un HTTP 400 esperado como prueba universal de cero invocaciones al socket sin instrumentación independiente.
- La verificación debe basarse preferentemente en identificación de build/versión de solo lectura ya existente, o en un entorno aislado con transporte físicamente sustituido/bloqueado.
- Si no existe un mecanismo seguro de identificación de versión y de bloqueo del transporte, el procedimiento queda **BLOCKED** hasta diseñar y aprobar uno. No añadir endpoints ni modificar código bajo este registro.

Esta corrección prevalece sobre cualquier frase del informe fuente que describa la sonda como “100% segura” o garantizada por el código que aún no está confirmado en memoria.

## 7. Barreras y condiciones de éxito/aborto

El informe declara preservados `ALLOW_REAL_DISPATCH=false` e `isAutoReply=0`. Estos valores deben verificarse en el entorno real antes de cualquier ventana, sin cambiarlos para facilitar pruebas.

Criterios propuestos:
- Código y diff revisados antes de comenzar.
- Backup SQLite consistente, verificado y con restauración definida, si corresponde.
- Procedimiento de recarga con responsables, prerrequisitos y rollback.
- Evidencia independiente de la versión activa corregida.
- Pruebas de bloqueo con transporte simulado o físicamente impedido.
- Cero invocaciones de transporte en los casos rechazados.
- Sin envío real como requisito de cierre de la validación de bloqueos.

Abortar si la versión activa no puede identificarse con seguridad, si el transporte no puede quedar impedido durante las sondas, si falla el backup/verificación, si aparece una llamada de transporte no autorizada o si se observa cualquier estado ambiguo de SQLite/WAL.

No restaurar automáticamente la base operativa como rollback genérico: primero preservar evidencia, detener nuevas acciones según el procedimiento autorizado y evaluar la recuperación con un plan compatible con SQLite y con la actividad que haya ocurrido.

## 8. Niveles de evidencia

| Nivel | Estado registrado |
|---|---|
| `STATIC VERIFIED` | Reportado por E.2.R.6; no revalidado independientemente en esta actualización |
| `HERMETIC VERIFIED` | 35/35 reportado; no reejecutado aquí |
| `RUNTIME VERIFIED` | `NOT VERIFIED` |
| `OPERATIONAL BLOCKING VERIFIED` | Reportado en pruebas aisladas; pendiente en vivo |
| `REAL WHATSAPP VERIFIED` | `NOT VERIFIED`; no autorizado |

## 9. Acciones que requieren autorización separada

No están autorizados por este registro:

- Detener/reiniciar `task-3480` o `task-3638`.
- Ejecutar la sonda propuesta contra el puerto 3002.
- Hacer backup/checkpoint o modificar la base operativa.
- Modificar variables de entorno.
- Habilitar el despacho real.
- Ejecutar E.3 o cualquier fase posterior.
- Enviar mensajes reales por WhatsApp.

## 10. Dictamen documental

- **Dictamen de la fuente:** `READY FOR AUTHORIZATION REVIEW`.
- **Dictamen tras revisión de seguridad del protocolo:** `READY FOR AUTHORIZATION REVIEW WITH BLOCKING CORRECTION`.
- **Gate operativo:** la sonda HTTP descrita en la sección 6 queda bloqueada hasta reemplazarla por un método que no pueda alcanzar el transporte en la versión antigua.
- **Estado de CRM E.2:** corrección E.2.R.5 y resultados de pruebas son reportados; runtime sigue `NOT VERIFIED`. No equivale a cierre operativo completo.

Este documento registra el informe aportado y añade una advertencia para evitar que la prueba de versión provoque el mismo tipo de despacho que el hardening pretende impedir. No se modificó el repositorio CRM-ALBRA, no se reiniciaron procesos y no se consultó ni alteró la base operativa durante esta actualización documental.
