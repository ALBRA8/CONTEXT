# ALBRA — Auditoría maestra de repositorios y proyectos
Fecha de corte: 2026-10-24
Estado: INVENTARIO Y DELTA REVIEW INICIADOS; NO ES CERTIFICACIÓN GLOBAL 100%.
Alcance: 17 repositorios públicos ALBRA8 accesibles y documentos de CONTEXT. Se revisaron metadata, commits recientes, README/manifiestos y archivos de raíz seleccionados. No se ejecutaron todas las suites ni se probaron todas las integraciones externas o despliegues.

## 1. Regla de lectura
- HEAD consultado: último commit recuperado por búsqueda; no implica despliegue ni certificación.
- Reportado/documentado no equivale a rerun independiente.
- Solo se escribe en ALBRA8/CONTEXT. Los repositorios de producto permanecen en modo lectura.

## 2. Inventario maestro

| Repositorio | Tipo / propósito observado | HEAD más reciente recuperado | Estado y hallazgos |
|---|---|---|---|
| [AGENTE-GENERAL](https://github.com/ALBRA8/AGENTE-GENERAL) | Seed universal técnico, cognitivo, memoria, seguridad, ejecución e integración | 69c79dad8337be156bcc60e74b53f98d5a993d54 (2026-10-22) | PARCIAL. La certificación del Seed congelado histórico no debe confundirse con el HEAD actual. Revisar ramas/HEAD y CI antes de certificar. |
| [AGENTE-LEADS](https://github.com/ALBRA8/AGENTE-LEADS) | Prospección e inteligencia de leads, Telegram, pipelines, evidencia, outreach y CRM hook | eb2b471dc6a8a856e41186332554441494b340b6 (2026-10-22) | PARCIAL. Delta: migración ARNÉS Fases 0–7; Fases 8–10 (LIPO, API routes, validación final) figuran pendientes en el commit. No asumir que el antiguo cierre de producción describe la migración nueva. Typecheck PASS reportado, no rerun. |
| [AGENTE-CHISMOSO](https://github.com/ALBRA8/AGENTE-CHISMOSO) | Plataforma de investigación/inteligencia con app Next y paquete chismoso | 5d5169ea975fa6b9f60664b8106cafaa1e4111fd (2026-10-22) | PARCIAL. Último commit informa fix P0 del orden de build Docker, prebuild y systemd. Declara aptitud interna single-tenant/VPN, no internet público sin WAF/DNS/monitorización/load test. Pruebas reportadas, no rerun. |
| [AGENTE-KREA](https://github.com/ALBRA8/AGENTE-KREA) | Product Factory y creación/empaquetado de productos digitales | 460605e5b4966e5e9243c1ec3fcb4852c6758e1b (2026-10-08) | PARCIAL. V2.1 condicional; 241 tests reportados, proveedores externos NOT_VERIFIED. **Hallazgo prioritario:** el listado público de la raíz contiene un archivo llamado .env. No se abrió ni copió su contenido. Verificar de forma segura; si contiene secretos, revocarlos/rotarlos y limpiar historial conforme al procedimiento. |
| [AGENTE-TRADING](https://github.com/ALBRA8/AGENTE-TRADING) | Análisis/operativa SMC, Telegram y bridge local MetaTrader 5 | 0559ac3975149d2ac6b4e0ea0080e18635f4b2f8 (2026-10-19) | PARCIAL. Pine v2.2 y breakerLevel descritos en commit. Sin backtest ni prueba MT5 en esta pasada; no inferir rentabilidad. |
| [CRM-ALBRA](https://github.com/ALBRA8/CRM-ALBRA) | CRM SaaS multi-tenant / CHALECO; contratos REST/MCP/MemoryDV para hosts | db95a5bec99bb85298f7ecbd6c630ebb8b60e669 (2026-10-22) | PARCIAL. Commit reporta estrategia consultiva en WhatsApp/Instagram/webhooks/Telegram y saneamiento del agente propietario; 381/381 tests, tsc y eslint PASS reportados, no rerun. Hallazgos locales del 24-oct (toNumber/LID y estado Git) no están demostrados como incorporados al HEAD remoto. Mantener despacho real desactivado hasta autorización expresa. |
| [YOUTUBE-AUTOMATION](https://github.com/ALBRA8/YOUTUBE-AUTOMATION) | Orquestador/fábrica audiovisual: producción, Flow, extensión, assets, render y entrega | f54f2adfe23b098b0fa50ee2cdc0f0fc587d3643 (2026-10-24) | PARCIAL. Incluye CONTEXTO YOUTUBE y ESTADO_REAL.md, que exige código y pruebas antes de declarar terminado. Último commit registra INT-035 en índice. Revalidar Flow/GoogleFlowMCP contra el HEAD actual; informes del 19–20-oct son históricos. |
| [NEX-SCOPE](https://github.com/ALBRA8/NEX-SCOPE) | SaaS de inteligencia de nichos de YouTube, tendencias, keywords, canales y planes; no clasificar automáticamente como agente puro | 2a69a6b8a6fe1e702b4e077482060b16ace59a5d (2026-10-22) | PARCIAL. Commit reporta correcciones SSRF/open proxy, rate limiting, headers, caché y build/standalone; no rerun. Integración social/SRZburner no demostrada ni autorizada. |
| [RADAR-SECOP2](https://github.com/ALBRA8/RADAR-SECOP2) | Inteligencia de oportunidades SECOP II, evidencia, análisis de pliegos, proyectos y MCP | 9f6a0d549cc31588cff621baa089fae62cde4692 (2026-10-22) | PARCIAL. Commit reporta push completo y CI verde; no se reejecutó CI ni se probó acceso real a fuentes, mensajería o MCP. |
| [Extension-CAZADOR](https://github.com/ALBRA8/Extension-CAZADOR) | Extensión/herramienta de extracción web y productos estructurados | b55f58f92b132f0be213cd2cf03123dcb5d891ad (2026-10-12) | PARCIAL. Commit reporta JSON-LD, microdata, OpenGraph, dedup conservadora, diagnóstico y shortcut sin IA; 130 tests/build/typecheck reportados, no rerun. No confundir con la extensión Flow Script Processor dentro de YouTube Automation. |
| [PURO-PODER-CONOCIMIENTO](https://github.com/ALBRA8/PURO-PODER-CONOCIMIENTO) | Corpus de marketing, consumidor, automatización, prompts, ventas y analítica | 9f1704b9ce8b3c992e48d0cb044b105d2e3e8904 (2026-10-07) | PARCIAL. Es un corpus de referencia; no se ha demostrado que sea un agente autónomo. Hay variantes de documentos/prompts que requieren distinguir originales, duplicados y derivados. |
| [DESPACHO-JURIDICO](https://github.com/ALBRA8/DESPACHO-JURIDICO) | Base técnica de organismo jurídico, evidencia/provenance, HITL, memoria, tools y arnés | 1bcf8290d06531cedb94d0638f4b0542991dcaea (2026-10-22) | PARCIAL. Commit reciente reporta arnés con 7 módulos de conocimiento y perfiles de 15 especialistas, 242/242 tests. README aún afirma conocimiento jurídico vacío y sin subagentes: documentación potencialmente desfasada. Conciliar con archivos reales antes de concluir. |
| [ERP-ADMON](https://github.com/ALBRA8/ERP-ADMON) | Marsella ERP administrativo/contable, independiente y complementario al CRM | 606d30e3c00ad3825bacfc81bdbc2a430dd82894 (2026-10-22) | PARCIAL. Package declara db:push --accept-data-loss y scripts con tee/comandos shell que requieren validar en Windows. No se probó DIAN real, firma, nómina ni contabilidad con datos reales. |
| [PANEL-DE-CONTROL](https://github.com/ALBRA8/PANEL-DE-CONTROL) | EEC / Enterprise Execution & Control Center: control plane, estado, comandos, eventos y aprobaciones | 71b46ef5a58795aa032474298829befa93031020 (2026-10-22) | PARCIAL. README describe provider local-sim y simulación honesta; no asumir integración real de agentes. Package declara db:push --accept-data-loss. Verificar auth/RBAC antes de exposición remota y revalidar integración con INSTALADOR tras su nuevo commit. |
| [INSTALADOR](https://github.com/ALBRA8/INSTALADOR) | Instalador/lifecycle de productos, fuentes GitHub, runtime y trust chain | 2313afea75452554b79d305bafd7091e77db3e00 (2026-10-22) | PARCIAL. Delta posterior a auditoría del 22-oct: commit declara runtime real, instalación por release/tag/commit, Ed25519 y contrato EEC autenticado, 18/18 PASS reportados. Win32 implementado pero UNTESTED; VPS y CA de firma pendientes; scripts aprobados diferidos. No rerun ni E2E con EEC. |
| [MAXI-APP](https://github.com/ALBRA8/MAXI-APP) | Aplicación web pequeña; README genérico sobre automatización | 24f493b63ddb83ceb3c23233135228d139b27598 (2026-04-13) | PARCIAL / poco conocimiento. Raíz muestra README, index.html y política de privacidad; no hay evidencia reciente de cierre. Confirmar si sigue activo, su función comercial y si es producto/prototipo/residual. |
| [CONTEXT](https://github.com/ALBRA8/CONTEXT) | Memoria documental canónica de ALBRA: decisiones, auditorías, agentes, proyectos, prompts y sesiones | d6774e9a46ebad90e9b4cf0a80a256c5fcaf1a47 (2026-10-24) | PARCIAL. La estructura existe, pero MIGRATION_MISSING_INFORMATION declara que no se auditó independientemente cada repositorio, faltan binarios históricos y no se duplicaron literalmente los cuerpos de prompts originales a GitHub. “Ready for operational use” significa usable con información recuperable, no 100% histórico. |

## 3. Hallazgos transversales

### P0 — posible exposición de secreto
AGENTE-KREA muestra un archivo .env en la raíz del repositorio público según el listado consultado. No se abrió su contenido. Verificarlo de forma segura; si contiene secretos, revocarlos/rotarlos inmediatamente y limpiar el historial. No copiar valores secretos a CONTEXT ni al informe.

### P1 — documentación desfasada o deltas sin reconciliar
- DESPACHO-JURIDICO: README y commit reciente describen estados distintos sobre conocimiento y especialistas.
- AGENTE-LEADS: ARNÉS Fases 0–7 con fases 8–10 pendientes; el cierre anterior no prueba que la migración nueva terminó.
- INSTALADOR ↔ PANEL-DE-CONTROL: la auditoría del 22-oct precede al commit V2 del instalador; revalidar contrato, auth, procesos, evidencia y E2E.
- CRM-ALBRA: HEAD remoto del 22-oct no incorpora automáticamente las correcciones locales/reportadas el 24-oct.
- YOUTUBE-AUTOMATION: el HEAD actual es posterior a informes del 19–20-oct; revisar el código actual para decisiones de Flow y GoogleFlowMCP.

### Riesgos operacionales
- CRM-ALBRA, ERP-ADMON y PANEL-DE-CONTROL tienen scripts db:push --accept-data-loss en los package consultados. No ejecutar contra datos valiosos sin respaldo y revisión explícita.
- Una ruta MCP, provider o endpoint no demuestra por sí solo auth, autorización ni integración de producción.
- Tests en mensajes de commit son reportados, no ejecución independiente de esta auditoría.

## 4. Alcance ejecutado y pendiente

Ejecutado:
- Enumeración de los 17 repositorios accesibles.
- Consulta de metadata, HEAD/commit reciente, archivos de raíz y README/manifiestos seleccionados.
- Detección de deltas relevantes frente a la matriz de contexto previa.
- Escritura limitada a CONTEXT.

Pendiente:
- Lectura de todos los archivos de todos los repositorios.
- Ejecución de todas las suites en entornos limpios.
- Pruebas de integraciones externas, WhatsApp/Telegram, Flow/Desktop, MT5, DIAN y despliegues.
- Inspección de clones/locales del PC.
- Verificación segura del .env público de KREA.
- No se puede afirmar todavía “auditoría 100% completa”.

## 5. Secuencia de cierre
1. Verificar la posible exposición de .env en AGENTE-KREA sin divulgar su contenido.
2. Leer diffs y archivos críticos de INSTALADOR, CRM-ALBRA, AGENTE-LEADS, DESPACHO-JURIDICO, AGENTE-CHISMOSO, YOUTUBE-AUTOMATION y NEX-SCOPE.
3. Auditar cada proyecto por arquitectura, seguridad, persistencia, APIs/contratos, tests/CI, despliegue, dependencias externas y documentación.
4. Ejecutar suites cuando el entorno permita reproducirlas y conservar logs verificables.
5. Conciliar fichas, decisiones, auditorías y proyectos en CONTEXT.
6. Inventariar Library/archivos y preservar originales, versiones y procedencia; no borrar conversaciones ni fuentes.
7. Actualizar la matriz final solo cuando cada fila tenga alcance, fecha, HEAD, evidencia y limitaciones.

## 6. Dictamen provisional
CONTEXT está estructurado, pero el ecosistema no está auditado al 100%. La prioridad documental es una ficha por proyecto con repositorio, estado, decisiones vigentes, evidencia, riesgos, pendientes e historia. La prioridad técnica inmediata es investigar el posible .env público de KREA y reconciliar los deltas recientes antes de emitir certificaciones de producción.
