# Actualización cruzada de repositorios — CRM-ALBRA, WHATSPOWER y WHATSPOWER-ULTRA

- **Fecha de revisión:** 2026-10-26
- **Repositorios consultados:** `ALBRA8/CRM-ALBRA`, `ALBRA8/WHATSPOWER`, `ALBRA8/WHATSPOWER-ULTRA`, además del contexto canónico `ALBRA8/CONTEXT`.
- **Alcance:** lectura de estado/documentación, commits recientes, README, manifiestos, árboles de archivos y código seleccionado. No se ejecutaron builds, suites, servidores, pruebas de integración ni envíos reales.
- **Escritura:** exclusivamente en `ALBRA8/CONTEXT`. No se modificaron los tres repositorios fuente.
- **Nivel de evidencia:** VERIFIED para los metadatos y archivos públicos consultados; VERIFIED DOCUMENTATION/REPORTED para resultados de pruebas o runtime que los commits e informes declaran, sin rerun independiente.

## 1. Modelo arquitectónico vigente

- **Nivel 1 — CRM-ALBRA:** fuente canónica de datos comerciales, contactos, oportunidades, pipeline, canales, permisos y autoridad del despacho físico a WhatsApp. La sesión Baileys vive en el daemon CRM.
- **Nivel 2 — WHATSPOWER:** motor de flujos conversacionales, Studio visual, reglas, entradas de usuario, secuencias, handoff/locks y herramientas para agentes/equipo.
- **Nivel 3 — WHATSPOWER-ULTRA:** campañas salientes, audiencias, Spintax, cola/cadencia, supresiones, atribución de clics y controles JIT. Debe consultar locks/supresiones de Nivel 2 cuando este esté habilitado; el modo standalone usa controles locales.
- Las capas son repositorios independientes y no deben duplicar la base comercial del CRM. La integración efectiva debe comprobarse por contratos, autenticación, aislamiento de organización, idempotencia, fallos y pruebas integradas; un README o un commit de features no prueba una integración E2E de producción.

## 2. HEAD público consultado

| Repositorio | Rama | HEAD público observado | Fecha del commit | Cambio principal descrito |
|---|---|---|---|---|
| [CRM-ALBRA](https://github.com/ALBRA8/CRM-ALBRA) | `main` | `db95a5bec99bb85298f7ecbd6c630ebb8b60e669` | 2026-10-22 | Estrategia comercial consultiva multicanal y saneamiento del agente propietario eliminado. El mensaje del commit reporta 381/381 tests, TypeScript sin errores y ESLint limpio; no se reejecutaron aquí. |
| [WHATSPOWER](https://github.com/ALBRA8/WHATSPOWER) | `main` | `d8547491630a3023013a2dd49dca31c7e18f9a9d` | 2026-10-26 | Round-robin de equipo, respuestas rápidas, growth links, triggers de etapa CRM y pasarela de pagos. |
| [WHATSPOWER-ULTRA](https://github.com/ALBRA8/WHATSPOWER-ULTRA) | `main` | `b7b5a5dabe7e84ecd3c74171dfaa0c9b75b340c9` | 2026-10-26 | Motor de neuromarketing, calibración cultural, metáforas/arquetipos y generador Spintax persuasivo. |

Los commits de los dos últimos repositorios del 26 de octubre incluyen cambios funcionales y agregan suites al script `npm test`; la existencia de tests no equivale a un resultado PASS en esta revisión.

## 3. CRM-ALBRA — estado y delta

### Estado público del repositorio
- README describe CRM SaaS multi-tenant con Next.js, Prisma/SQLite, daemon Baileys en puerto 3002, contratos REST `/api/crm/actions`, MCP `/api/mcp` y contrato de memoria.
- El último commit público visible en `main` es de 2026-10-22. El código del commit incorpora `SALES_STRATEGY_PROMPT` a WhatsApp, Instagram/webhooks, espejo channel-agent y Telegram, y reporta eliminación del agente de chat propietario.
- `package.json` contiene `db:push` con `--accept-data-loss`; no usarlo sin revisar el entorno/datos y hacer backup. El README y el informe de preparación productiva describen guardas de despliegue, pero no sustituyen un gate de esquema destructivo.
- La ruta pública `src/whatsapp-daemon/daemon.mjs` existe. La ruta `src/whatsapp-daemon/dispatch-gate.mjs` consultada devuelve 404 en `main`; por tanto, el trabajo D.5/E.2.R descrito en informes de estado local **no queda demostrado como incorporado al HEAD público**. El archivo y las suites D.5 no deben tratarse como parte de `main` sin una nueva comprobación del árbol/branch o un commit que los incluya.

### Readiness operacional documentada en CONTEXT
- [Baseline Prisma y standalone](../03_AUDITS/CRM_ALBRA_PRISMA_BASELINE_AND_STANDALONE_READINESS_2026-10-26.md) reporta baseline `0_init`, esquema al día, `PRAGMA integrity_check: ok`, build standalone, Next.js :3000 y daemon :3002 saludables, cero despachos durante la intervención y `ALLOW_REAL_DISPATCH=false`.
- [Production Readiness](CRM_ALBRA_PRODUCTION_READINESS_2026-10-26.md) clasifica `READY FOR PRODUCTION DEPLOYMENT`, no `PRODUCTION DEPLOYED — VERIFIED`. Host definitivo, supervisor de procesos, despliegue externo, TLS/monitorización y ventana de despacho real siguen pendientes/no autorizados.
- El runtime y la base local provienen de informes del propietario/Antigravity; no se inspeccionaron desde esta revisión de GitHub.
- E.2.R.6 conserva una advertencia de seguridad: no enviar una sonda inválida a un daemon potencialmente antiguo mientras la versión en memoria no se haya identificado de forma segura. No reiniciar servicios ni tocar SQLite/WAL/SHM por esta actualización.

## 4. WHATSPOWER — Nivel 2

### Código y capacidades observadas
- README declara Flow Studio `/studio`, motor de flujos con mensaje/condición/acción, User Input, tags/campos, secuencias drip, handoff y supresiones.
- El commit `2e23cbc73518e23cb465449b9e4d1436c34e097a` añade/describe paridad de flujo visual, secuencias, User Input e integración plug-and-play con CRM.
- El HEAD `d854749...` agrega tablas y módulos para equipo/round-robin, canned responses, growth links, triggers de etapa CRM y links de pago. El script de tests incluye `tests/superpowers-level2.test.ts`.
- El servidor importa `MockTransportSender` por defecto; el transporte HTTP al CRM debe configurarse explícitamente. La existencia de los adaptadores no demuestra que el entorno activo esté conectado al CRM real.
- `PaymentGatewayManager` genera URLs por pasarela; el código consultado contiene identificadores/URLs de prueba y el método de aprobación actualiza el estado/tag localmente. **No considerar Wompi/Mercado Pago/Stripe como integración de cobro productiva verificada** sin credenciales reales, firma de webhook, validación de referencia/importe/moneda y pruebas de callbacks autorizados.
- `FICHA.md` en este repositorio conserva un texto anterior de diseño exclusivo y “sin código funcional implementado”, además de una ruta histórica `clones/albra-agentic-2a/`. Esto contradice el README y el código actuales del Nivel 2. Es deuda documental; no asumir que esa ficha refleja el estado del repositorio.

### Riesgos visibles para revisar antes de exponer el servicio
- `src/server.ts` incluye fallback de secreto HMAC de desarrollo. Debe fallar cerrado en entornos no locales/producción si no hay secreto explícito y fuerte.
- La verificación de HMAC del inbound es condicional en el código observado: si no hay firma y `NODE_ENV` no es `production`, la ruta puede continuar sin verificarla. Confirmar que el servicio nunca se exponga en ese modo y hacer que el contrato sea explícito.
- La ruta de liberación de lock `POST /api/internal/locks/:phone/release` observada no muestra comprobación de secreto/autorización antes de liberar el lock. Revisar y corregir antes de cualquier exposición de red.
- Varias rutas de administración/flujo y módulos de equipo/pagos deben tener autenticación, autorización por organización, límites de tamaño/tiempo y auditoría antes de operar como servicio multi-tenant accesible desde red. Esta revisión estática no constituye un pentest completo.
- Las respuestas de pago, asignación y triggers no deben mutar la base comercial directamente; los cambios comerciales deben seguir los contratos autorizados del CRM.

## 5. WHATSPOWER-ULTRA — Nivel 3

### Código y capacidades observadas
- README declara Campaign Studio `/campaigns`, CSV con normalización telefónica, motor Spintax, cadencia/cooldown, supresión, chequeo JIT de locks, tracking de enlaces y recuperación ante caídas.
- `a85bcced...` establece la base del Nivel 3; `4937e20...` agrega ingesta de leads por webhook, atribución de clics y adaptador Meta Cloud; `b7b5a5d...` agrega el motor de neuromarketing y el script incluye `tests/neuromarketing-engine.test.ts`.
- La configuración por defecto del servidor utiliza `HttpCrmDaemonDispatcher({ allowRealDispatch: false })`; el adaptador Meta Cloud también usa modo mock si no hay token o si `META_MOCK_MODE=true`. La presencia del adaptador no demuestra envío real con Meta.
- El `phoneNumberId` por defecto es un placeholder. Para producción se requieren configuración, credenciales, permisos, versión API vigente y pruebas separadas.
- La ingesta de leads consulta la lista de supresión local y puede añadir contactos a campañas. La firma/autenticación del webhook, el aislamiento por tenant, la procedencia/consentimiento del lead y la idempotencia del evento requieren verificación antes de exposición pública.
- El tracking persiste IP, user-agent y, cuando se suministra, contacto; definir minimización, retención, acceso y aviso de privacidad.
- La etiqueta “anti-ban”/cadencia no garantiza evitar bloqueos. El producto debe priorizar consentimiento, opt-out, límites de frecuencia y políticas de WhatsApp/Meta; Spintax no debe utilizarse para evadir controles antispam.
- El código del motor de neuromarketing emplea heurísticas de país/industria y catálogos estáticos. Presentarlo como generador heurístico de recomendaciones, no como medición validada de psicología subconsciente ni como eficacia de conversión demostrada.

### Gate de seguridad
- No activar `ALLOW_REAL_DISPATCH` ni campañas reales como consecuencia de la incorporación de features.
- Verificar autorización del endpoint, autenticidad de webhooks, supresiones, locks JIT, idempotencia distribuida, resultados ambiguos, cancelación y recuperación de colas.
- La prueba con `MockDispatcher` no demuestra la entrega externa; el envío mediante daemon CRM y la modalidad Meta Cloud requieren pruebas separadas y explícitamente autorizadas.

## 6. Estado de integración entre las tres capas

| Integración | Estado prudente tras esta revisión |
|---|---|
| CRM-ALBRA ↔ WHATSPOWER inbound/acciones CRM | Contratos y adaptadores existen en código/documentación; integración de producción E2E no verificada aquí. |
| WHATSPOWER ↔ WHATSPOWER-ULTRA locks | Cliente/checker y ruta de lock descritos; autenticación/contrato efectivo debe probarse. El endpoint de liberación de lock visible en WHATSPOWER es un hallazgo prioritario. |
| WHATSPOWER-ULTRA ↔ daemon CRM | Adaptador HTTP existe y el despacho real está bloqueado por defecto; entrega E2E no verificada. |
| WHATSPOWER-ULTRA ↔ Meta Cloud API | Adaptador de código presente, modo mock por defecto sin token; integración real no verificada. |
| Pagos de WHATSPOWER | Generación de enlaces y estado local presentes; cobro/callback productivo no verificado. |
| Prisma/D.5/E.2.R en CRM | Informes de entorno local no prueban que los cambios estén en el HEAD público de `main`; `dispatch-gate.mjs` no se encontró en esa rama. |

## 7. Siguientes pasos recomendados

1. **No abrir los servidores a Internet** hasta cerrar autenticación/autorización y secretos por defecto en WHATSPOWER y WHATSPOWER-ULTRA.
2. Reconciliar `WHATSPOWER/FICHA.md` con el código real de Nivel 2 y conservar claramente qué es diseño histórico versus implementación actual.
3. Resolver la divergencia entre CRM-ALBRA `main` y el estado D.5/E.2.R local reportado; no copiar el veredicto local a la rama pública sin revisar commits/diffs.
4. Ejecutar las suites en un entorno limpio y registrar salida real/commit; luego realizar pruebas de contrato con mocks/no-op para las tres capas.
5. Verificar firma y autenticación de todos los webhooks/acciones, aislamiento multi-tenant, límites de cuerpo, protección de locks, idempotencia y recuperación de resultados ambiguos.
6. Validar por separado credenciales, callbacks y referencias de pagos; no anunciar pasarelas productivas hasta tener pruebas con sandbox oficial.
7. Mantener envíos reales deshabilitados hasta que el propietario autorice una ventana supervisada específica.

## Conclusión

Los tres repositorios evolucionaron sustancialmente: CRM mantiene el papel de fuente comercial/transportista, WHATSPOWER concentra la automatización conversacional y WHATSPOWER-ULTRA amplía campañas/atribución/neuromarketing. Los HEAD y archivos públicos permiten confirmar el estado de código y los cambios descritos, pero no prueban que todos los servicios estén conectados o desplegados.

**No se ejecutó código, no se modificaron repositorios de producto y no se enviaron mensajes.** Los hallazgos de seguridad anteriores son observaciones estáticas que requieren validación/pruebas; no son una auditoría exhaustiva ni una certificación de producción.
