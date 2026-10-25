# ALBRA — CRM-ALBRA: auditoría final y cierre controlado de Fase D.5

- **Fecha de registro:** 2026-10-25
- **Repositorio fuente declarado:** `ALBRA8/CRM-ALBRA`
- **Estado de cierre reportado:** `VERIFIED WITH LIMITATIONS`
- **Fuente:** informe final de auditoría aportado por el propietario/Antigravity en esta conversación.
- **Nivel de evidencia de este documento:** documentación del informe de ejecución; los comandos y resultados se registran como reportados por Antigravity, no reejecutados independientemente desde esta actualización de CONTEXT.
- **Alcance de escritura:** documentación únicamente en `ALBRA8/CONTEXT`. No se modificó el repositorio de producto ni el entorno local.

## 1. Resumen ejecutivo

El informe declara que la Fase D.5 endureció la adquisición de despacho controlado mediante una transacción SQLite que agrupa: cambio condicional de estado (CAS), inserción del nonce consumido y sello del nonce en metadatos. Se reportan pruebas de rollback, concurrencia, persistencia anti-replay y fail-closed.

La conclusión se conserva como **`VERIFIED WITH LIMITATIONS`**, no como verificación integral de producción. La integración con el query engine real de Prisma no fue probada; la entrega externa E2E a WhatsApp se mantuvo intencionalmente sin ejecutar.

## 2. Estado de Git y alcance declarado

El informe indica:

- Rama: `main`.
- Commit base: `5954cd00e3bcd31c2abb9dab145b568e6e22cf2c` (`fix(whatsapp): correct inbound toNumber persistence`).
- `git diff --check`: código de salida 0.
- Cero commits nuevos, push, merges o despliegues durante la tarea reportada.
- Cero archivos restaurados o descartados; las modificaciones permanecen en el árbol de trabajo.

### Archivos modificados reportados

- `src/whatsapp-daemon/dispatch-gate.mjs`
- `src/whatsapp-daemon/daemon.mjs`
- `src/app/api/timeline/route.ts`
- `src/app/api/whatsapp/auto-reply/route.ts`
- `src/components/whatsapp/whatsapp-page.tsx`
- `src/lib/api.ts`
- `package-lock.json`

### Archivos nuevos no rastreados reportados

- `tests/unit/d5-transactional-atomic-dispatch.test.mjs` — suite de 14 pruebas.
- `scripts/check-baseline.mjs` — consulta de línea base en modo de solo lectura.

Esta lista describe el estado declarado por el informe, no un `git status` inspeccionado independientemente en CONTEXT.

## 3. Atomicidad y concurrencia

### Transacción SQLite

El informe identifica `executeAtomicDispatchAcquisition` en `dispatch-gate.mjs` como la ruta que agrupa tres operaciones en una transacción:

1. CAS de la propuesta desde `approved` hacia `dispatching`.
2. Inserción del nonce en `ConsumedDispatchNonce`.
3. Actualización de `metadata.consumedTicketNonce`.

Los Tests 2 y 3 reportados inyectan fallos durante los pasos 2 y 3 y luego consultan SQLite para comprobar que el estado vuelve a `approved` y que el nonce no queda persistido.

### Competencia concurrente

Los Tests 4 y 5 reportados utilizan `Promise.all` sobre dos llamadas a `executeControlledDispatch`, con una base SQLite aislada y un único objeto de conexión `better-sqlite3` en modo WAL.

El informe distingue correctamente estos niveles:

- Una adquisición transaccional ganadora en la base aislada.
- Una invocación a un `sendFn` simulado en memoria.
- Cero llamadas reales a `sock.sendMessage` de Baileys.
- Cero tráfico real a WhatsApp y cero registros nuevos en la base operativa atribuibles a esa prueba.

La respuesta del perdedor puede variar por la capa en que se detecte la carrera: `409` si pierde el CAS dentro de la transacción, o `400` si el pre-chequeo observa que la propuesta ya no está en estado `approved`. Para el mismo nonce previamente persistido, el gate reporta `403`; el daemon reporta `423` cuando detecta replay en su memoria. Estos contratos por capa deben mantenerse explícitos en las pruebas y documentación.

## 4. Matriz de códigos HTTP reportada

| Escenario | Respuesta reportada | Condición/capa |
|---|---|---|
| CAS perdido | `409` o `400` | `409` si el CAS actualiza cero filas; `400` si la validación previa encuentra estado no permitido. |
| Nonce previamente consumido | `403` en gate; `423` en daemon | Replay detectado en SQLite por el gate o en memoria por el daemon. |
| Colisión de clave única del nonce | `409` | Restricción UNIQUE de SQLite. |
| Ticket ausente/inválido | `403` o `423` | Ticket ausente o inválido en gate; ausencia de `dispatchTicket` en el endpoint del daemon. |
| Despacho global bloqueado | `423` | `ALLOW_REAL_DISPATCH=false`. |
| Hash de texto incorrecto | `400` o `403` | `400` en validación de propuesta; `403` en validación de ticket/daemon. |
| Estado de propuesta no permitido | `400` o `409` | `400` para estados no despachables; `409` para estado ambiguo reportado. |
| Base de datos ausente/adaptador incompatible | `500` | Rechazo fail-closed. |

Los códigos no son universales para cada concepto: la respuesta depende de la ruta y del punto de validación. Cualquier cambio posterior debe preservar compatibilidad con los consumidores de la API y acompañarse de pruebas de contrato.

## 5. Recuperación ante fallos entre COMMIT y envío

El informe documenta tres escenarios. No se ejecutaron envíos reales para probarlos.

| Escenario | Estado local reportado | Certeza de entrega | Recuperación reportada y limitación |
|---|---|---|---|
| A. Caída tras COMMIT y antes de invocar Baileys | Propuesta en `dispatching`; nonce consumido en SQLite | Si se demuestra que el socket nunca fue invocado, no hubo envío desde ese proceso. Debe confirmarse mediante evidencia de ejecución/logs. | Revisión administrativa y reapertura autorizada con ticket/nonce nuevo. No existe reconciliador autónomo de propuestas huérfanas. |
| B. Caída tras invocar Baileys y antes de persistir confirmación | Propuesta en `dispatching`; nonce consumido | **Ambigua:** el proveedor pudo haber aceptado el mensaje aunque no exista confirmación local. | Reconciliación manual; no reintentar a ciegas. El informe propone consultar WhatsApp y registrar el ID si aparece. La inspección visual no se presenta como mecanismo automatizado ni garantía distribuida. |
| C. Fallo explícito de red/socket tras adquirir ticket | El informe declara estado `failed` y `lastError`; nonce permanece consumido | Depende de la naturaleza del error. Un rechazo inequívoco previo a la aceptación puede indicar no entrega; un timeout o error ambiguo no lo demuestra. | Nueva aprobación formal antes de emitir otro ticket; no se reporta reintento automático. |

**Riesgo residual:** la atomicidad SQLite protege la consistencia local, pero no hace atómico el envío externo a WhatsApp. No existe evidencia de un worker autónomo de reconciliación ni una garantía de entrega exactamente una vez. Los mensajes con resultado ambiguo requieren un procedimiento operativo explícito antes de considerar un reenvío.

## 6. Prisma real: no verificado

El informe declara que el Test 12 de `d5-transactional-atomic-dispatch.test.mjs` usa `prismaMockDb`, que reproduce `$transaction` y `$executeRawUnsafe` sobre SQLite mediante sentencias directas `BEGIN IMMEDIATE`, `COMMIT` y `ROLLBACK`.

Según el análisis reportado, ninguna prueba de `tests/` instancia el `PrismaClient` real.

**Estado: `NOT VERIFIED`.** La prueba respalda el contrato simulado de la interfaz, pero no demuestra la interacción con el query engine de `@prisma/client` en el runtime de Next.js. La integración real queda pendiente y no debe describirse como aprobada.

## 7. Línea base operativa reportada

El script `node scripts/check-baseline.mjs` habría consultado `db/custom.db` en modo de solo lectura. Resultados declarados:

- `WhatsAppMessage`: 8 registros en total.
- `direction = 'outbound'`: 3 mensajes históricos.
- `direction = 'OUTBOUND'`: 0 registros, según la consulta reportada.
- Propuesta `cmvmr0j6c1qvmjt`: `status = proposed`, `dispatch = blocked`, `source = agent`.
- Conversación objetivo `cmvm4vp03soktvf`: `isAutoReply = 0`.
- `AGENTIC_2A_ENABLED=false`.
- `ALLOW_REAL_DISPATCH=false`.
- Cero mensajes nuevos y cero llamadas reales a Baileys reportadas.

Estos valores se conservan como resultados del informe aportado; esta actualización documental no consultó directamente la base de datos local.

## 8. Pruebas y comandos reportados

| Comando | Resultado reportado |
|---|---|
| `node tests/unit/d5-transactional-atomic-dispatch.test.mjs` | Exit 0; 14/14 PASS. |
| `node tests/unit/dispatch-gate.test.mjs` | Exit 0; PASS. |
| `node tests/unit/controlled-dispatch.test.mjs` | Exit 0; 13/13 PASS. |
| `node tests/unit/d21-persistence-anti-replay.test.mjs` | Exit 0; 8/8 PASS. |
| `node tests/unit/d4-atomicity-failure-recovery.test.mjs` | Exit 0; 8/8 PASS. |
| `node scripts/check-baseline.mjs` | Exit 0; 3 salientes históricos, propuesta `proposed/blocked`, `isAutoReply=0`. |
| `git diff --check` | Exit 0; salida limpia. |

Los resultados son **reportados por Antigravity**. No se reejecutaron desde esta actualización de CONTEXT.

## 9. Evaluación final de controles

| Control | Estado documental | Nota |
|---|---|---|
| Transacción atómica SQLite con `better-sqlite3` | `VERIFIED — según evidencia reportada` | Tests aislados y rollback post-fallo reportados. |
| CAS y exclusión mutua | `VERIFIED — según evidencia reportada` | Concurrencia competitiva en base aislada reportada. |
| Persistencia anti-replay tras reinicio | `VERIFIED — según evidencia reportada` | Reapertura de conexión/archivo SQLite reportada. |
| Fail-closed por ausencia de BD/ticket | `VERIFIED — según evidencia reportada` | Pruebas y códigos HTTP reportados. |
| Contrato de daemon antes del socket | `VERIFIED — según evidencia reportada` | No se ejecutó un E2E de red. |
| Integración con PrismaClient real | `NOT VERIFIED` | El test usa un mock. |
| Entrega externa real a WhatsApp (E2E) | `NOT VERIFIED` | Intencionalmente no ejecutada. |
| Línea base operativa | `VERIFIED — según evidencia reportada` | Conteos y estado de propuesta informados mediante consulta de solo lectura. |
| Árbol Git local | `REPORTED` | El estado local no fue inspeccionado independientemente en esta actualización. |

## 10. Gate de seguridad y siguientes pasos

1. Mantener `ALLOW_REAL_DISPATCH=false` y `AGENTIC_2A_ENABLED=false` hasta autorización expresa separada.
2. No aprobar ni despachar la propuesta `cmvmr0j6c1qvmjt` como parte de esta auditoría.
3. Mantener documentada la variación de códigos HTTP por capa; cualquier cambio debe tener pruebas de contrato.
4. Definir y autorizar por separado un procedimiento de recuperación de propuestas huérfanas y resultados ambiguos antes de permitir reintentos operativos.
5. Probar la integración real de Prisma en entorno aislado antes de declarar esa rama verificada.
6. No confundir tests unitarios satisfactorios con entrega E2E o con garantía de entrega exactamente una vez.
7. No iniciar la siguiente fase ni habilitar tráfico real como consecuencia automática de este cierre.

## Conclusión

**`VERIFIED WITH LIMITATIONS` — estado reportado para la Fase D.5.**

El informe aporta evidencia reproducible declarada para atomicidad local SQLite, rollback, CAS, anti-replay y fail-closed. Permanecen sin verificar la integración real con Prisma y la entrega externa E2E. También queda pendiente un reconciliador autónomo para propuestas huérfanas y resultados ambiguos. La presente entrada registra el informe y sus límites; no constituye una auditoría independiente del código ni de la base operativa.
