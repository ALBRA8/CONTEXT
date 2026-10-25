# CRM-ALBRA — Auditoría final y cierre controlado de Fase D.5

- **Fecha del informe fuente:** 2026-10-25
- **Estado declarado por el informe:** `VERIFIED WITH LIMITATIONS`
- **Procedencia:** informe proporcionado por el propietario en esta conversación.
- **Alcance de este registro:** preservar los resultados, matrices y limitaciones declaradas. La incorporación a CONTEXT no constituye una repetición independiente de pruebas, inspección del árbol local ni verificación de producción.
- **Privacidad:** se omiten del registro público el número telefónico y datos personales directos que aparecían en la salida de la base operativa. Los IDs técnicos no se consideran prueba independiente de que la base siga en ese estado.

## 1. Estado de Git reportado

El informe fuente declara:
- Rama `main`, exactamente un commit por delante de `origin/main`.
- Commit base `5954cd00e3bcd31c2abb9dab145b568e6e22cf2c` (`fix(whatsapp): correct inbound toNumber persistence`).
- `git diff --check`: código de salida 0.
- Cero commits nuevos, pushes, merges, despliegues y archivos restaurados/descartados durante la sesión de auditoría.
- Los cambios quedaron en el árbol de trabajo y no se hizo commit de producto.

Estos puntos son **declaraciones del informe fuente**; esta actualización de CONTEXT no inspeccionó el árbol local ni el remoto del repositorio de producto.

## 2. Archivos de producto que el informe declara inspeccionados/modificados

### Árbol de trabajo
- `src/whatsapp-daemon/dispatch-gate.mjs`: transacción SQLite atómica `executeAtomicDispatchAcquisition`, eliminación de compensaciones sueltas, fail-closed ante BD ausente/ticket ausente y verificación de `textHash`.
- `src/whatsapp-daemon/daemon.mjs`: comparación de hash entre ticket, texto aprobado y texto recibido por `/send`; validación independiente en SQLite antes de Baileys; anti-replay en memoria.
- `src/app/api/timeline/route.ts`: enrutamiento exclusivo de `action: 'dispatch'` hacia `executeControlledDispatch`.
- `src/app/api/whatsapp/auto-reply/route.ts`: aislamiento server-to-server.
- `src/components/whatsapp/whatsapp-page.tsx`: interfaz de revisión y aprobación.
- `src/lib/api.ts` y `package-lock.json`.

### Archivos no rastreados reportados
- `tests/unit/d5-transactional-atomic-dispatch.test.mjs`: suite de 14 pruebas declaradas de atomicidad, concurrencia, fallos forzados y anti-replay.
- `scripts/check-baseline.mjs`: consulta de solo lectura sobre `db/custom.db`.

No se modificaron esos archivos durante la actualización documental de CONTEXT.

## 3. Matriz HTTP por escenario (según el informe)

| Escenario | Respuesta documentada | Capa/condición reportada |
|---|---|---|
| CAS perdido por concurrencia | 409 o 400 | 409 si el CAS de `executeAtomicDispatchAcquisition` actualiza cero filas; 400 si el precheck ve que la propuesta ya no está en estado despachable. |
| Nonce previamente consumido | 403 (gate) o 423 (daemon) | Gate consulta persistencia SQLite; daemon consulta su memoria local anti-replay. |
| Colisión UNIQUE del nonce | 409 | Captura de `UNIQUE constraint failed: ConsumedDispatchNonce.nonce`. |
| Ticket ausente/inválido | 403 o 423 | Gate rechaza nonce ausente, firma HMAC inválida o TTL vencido; daemon rechaza `dispatchTicket` ausente. |
| Despacho global bloqueado | 423 | `executeControlledDispatch` observa `isAllowed === false` con `ALLOW_REAL_DISPATCH=false`. |
| Hash de texto incorrecto | 400 o 403 | 400 en precheck de propuesta; 403 en ticket/daemon cuando el hash firmado no coincide byte a byte. |
| Propuesta en estado no permitido | 400 o 409 | 400 para `proposed`, `edited`, `discarded` o `rejected`; 409 si `ambiguous: true`. |
| BD ausente o adaptador incompatible | 500 | Fail-closed si falta la BD o el adaptador no ofrece transacción. |

La matriz muestra que la respuesta depende de la capa y del punto de validación. La compatibilidad del contrato HTTP debe comprobarse en los consumidores; no debe asumirse un único código para todas las rutas de rechazo.

## 4. Recuperación ante crash — análisis declarado

El informe estudia tres escenarios sin realizar envíos reales:

| Escenario | Estado y nonce reportados | Certeza de entrega declarada | Procedimiento seguro documentado | Limitación |
|---|---|---|---|---|
| A. Crash después del COMMIT y antes de llamar a Baileys | Propuesta `dispatching`; nonce consumido y persistido en SQLite | El informe declara no entrega porque el socket no se habría abierto | Revisar logs; reabrir administrativamente a `approved` y emitir ticket nuevo con nonce nuevo | No existe worker autónomo para huérfanos |
| B. Crash después de invocar Baileys y antes de guardar confirmación | Propuesta `dispatching`; nonce consumido y persistido | Ambigüedad total: Meta pudo recibir el mensaje sin confirmación local | Reconciliación manual; comprobar WhatsApp Web y registrar ID si aparece; solo reabrir si se determina que no salió | No existe reconciliación automática; reintentar a ciegas puede duplicar |
| C. Fallo explícito de red/socket tras adquirir ticket | Propuesta `failed`, `lastError` guardado; nonce consumido | El informe declara no entrega cuando el socket rechazó la conexión | Nueva aprobación formal desde la interfaz antes de emitir otro ticket | No hay reintento automático |

Los procedimientos de reapertura administrativa son parte del informe, no una acción ejecutada por esta actualización. La atomicidad de SQLite no hace atómicos el COMMIT local y la entrega externa. Ante un resultado ambiguo no debe haber reintento ciego.

## 5. Integración real con Prisma

El informe declara que se inspeccionaron las pruebas del repositorio mediante búsqueda automatizada y que **ninguna prueba instancia o ejecuta un `PrismaClient` real**.

El Test 12 de `d5-transactional-atomic-dispatch.test.mjs` utiliza `prismaMockDb`, que reproduce la interfaz `$transaction` / `$executeRawUnsafe` y ejecuta SQL directamente sobre SQLite.

**Estado: `NOT VERIFIED`** para la integración con el query engine de `@prisma/client` bajo Next.js en runtime.

## 6. Pruebas y comandos reportados por la sesión fuente

| Comando | Resultado declarado por el informe |
|---|---|
| `node tests/unit/d5-transactional-atomic-dispatch.test.mjs` | Exit 0; 14/14 PASS; transacción, rollback, concurrencia, reinicio, anti-replay, fail-closed y constraints. |
| `node tests/unit/dispatch-gate.test.mjs` | Exit 0; PASS. |
| `node tests/unit/controlled-dispatch.test.mjs` | Exit 0; 13/13 PASS. |
| `node tests/unit/d21-persistence-anti-replay.test.mjs` | Exit 0; 8/8 PASS. |
| `node tests/unit/d4-atomicity-failure-recovery.test.mjs` | Exit 0; 8/8 PASS. |
| `node scripts/check-baseline.mjs` | Exit 0; línea base consultada en modo de solo lectura. |
| `git diff --check` | Exit 0; limpio. |

El informe identifica las bases aisladas `test-d5-isolated.db`, `tests/unit/test-d21.db` y `tests/unit/test-d4.db` para las suites correspondientes. **Los comandos no se reejecutaron al registrar este documento**; sus resultados se conservan como reportados.

## 7. Línea base operativa declarada

Según la salida que incluye el informe fuente:
- Total de mensajes: 8.
- Mensajes históricos con `direction = 'outbound'`: 3; con `direction = 'OUTBOUND'`: 0.
- Propuesta técnica `cmvmr0j6c1qvmjt`: `status: proposed`, `dispatch: blocked`, `source: agent`.
- Conversación objetivo: `isAutoReply=0`.
- `ALLOW_REAL_DISPATCH=false` y cero mensajes nuevos durante la sesión auditada, según el informe.

Los números telefónicos que aparecían en la salida original se omitieron de este registro público. **Esta actualización no consultó `db/custom.db`**, por lo que estos datos se consideran reportados y no revalidados independientemente.

## 8. Evaluación de controles según el informe fuente

| Control | Estado del informe | Límite |
|---|---|---|
| Atomicidad de las tres escrituras SQLite con `better-sqlite3` | `VERIFIED` | Según pruebas reportadas; no rerun independiente aquí. |
| Rollback ante fallos forzados en pasos 2 y 3 | `VERIFIED` | Estado post-fallo en disco reportado por la suite. |
| CAS y concurrencia | `VERIFIED` | Dos solicitudes en `Promise.all`; resultado reportado. |
| Anti-replay persistente tras reinicio | `VERIFIED` | Test con persistencia SQLite reportado. |
| Gate del daemon `/send` antes del socket | `VERIFIED` | Inspección y pruebas declaradas; sin E2E de red. |
| Integración con PrismaClient real | `NOT VERIFIED` | El test usa un mock. |
| Entrega externa E2E a WhatsApp | `NOT VERIFIED` | No ejecutada por la salvaguarda de despacho. |
| Línea base operativa | `VERIFIED` según el informe | La consulta se declara ejecutada, pero no se repitió en esta sesión documental. |
| Estado del árbol Git local | `REPORTED` | No inspeccionado independientemente aquí. |

## 9. Riesgos residuales y gates de seguridad

1. Mantener `ALLOW_REAL_DISPATCH=false` y `AGENTIC_2A_ENABLED=false` hasta autorización expresa separada.
2. No aprobar ni despachar la propuesta objetivo como parte de este registro.
3. Mantener explícita la variación de códigos HTTP por capa y probar el contrato de consumidores.
4. Definir un procedimiento de recuperación de propuestas huérfanas y resultados ambiguos antes de permitir reintentos operativos.
5. Probar `PrismaClient` real en entorno aislado antes de declarar verificada esa integración.
6. No confundir suites unitarias con entrega E2E ni con garantía de entrega exactamente una vez.
7. No iniciar la siguiente fase ni habilitar tráfico real como consecuencia automática de este cierre.

## Conclusión

**`VERIFIED WITH LIMITATIONS` — clasificación declarada por el informe fuente.**

El informe aporta resultados reproducibles declarados para atomicidad local SQLite, rollback, CAS, anti-replay y fail-closed. Siguen sin verificarse la integración real con Prisma y la entrega externa E2E. También queda pendiente un reconciliador autónomo para propuestas huérfanas y resultados ambiguos.

Este documento registra la evidencia aportada y sus límites; no es una auditoría independiente del código, del árbol Git local, de la base operativa ni de producción.
