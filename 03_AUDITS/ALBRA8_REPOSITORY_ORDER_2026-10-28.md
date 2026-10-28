# ALBRA8 — Inventario y orden de trabajo
Fecha de corte: 2026-10-28
Fuente: repositorios visibles en GitHub y último commit devuelto por búsqueda de commits. Este documento es un inventario operativo inicial; no equivale a auditoría completa de código, CI ni producción.

## Reglas de trabajo
1. No ejecutar pruebas, campañas, envíos ni cambios de runtime hasta que se acuerde explícitamente una fase de pruebas.
2. No modificar repositorios de producto sin autorización explícita.
3. Separar siempre: documentado / implementado según código / pruebas reportadas / pruebas reproducidas en esta sesión / validado en producción.
4. La descripción de un commit es una declaración del commit, no evidencia independiente de que las pruebas hayan pasado.
5. No se pudo confirmar CI para varios commits mediante estados combinados: respuesta vacía significa “sin estado devuelto”, no “verde” ni “rojo”.

## Inventario de 23 repositorios
| Repositorio | Último commit visible (UTC) | Tema del último commit / propósito tentativo | Prioridad |
|---|---|---|---|
| CRM-ALBRA | 2026-10-27 20:38 — ea87cc2 | Daemon WhatsApp: responder al JID real; commit declara sandbox E2E y 390 tests | P0 — núcleo comercial |
| WHATSPOWER | 2026-10-28 00:36 — f4f7fc9 | Fallback de secreto para suite de tests CP-11 | P0 — capa 2A y contratos |
| WHATSPOWER-ULTRA | 2026-10-27 17:05 — e6ef56d | Fail-closed para INTERNAL_API_SECRET en piloto y tests; commit declara 19/19 | P0 — capa 2B y despacho |
| CHAT-X | 2026-10-28 04:32 — fa2fffcc | Plataforma central con seis módulos e integraciones declaradas | P0 — verificar contratos antes de ser centro de operaciones |
| AGENTE-GENERAL | 2026-10-22 16:59 — 69c79da | Merge de host de skills y regresión | P0 — motor/controles transversales |
| DESPACHO-JURIDICO | 2026-10-28 07:06 — 392f416 | Fix de tests con Playwright opcional; el commit cita CI anterior con 2 fallos | P0 — confirmar CI posterior |
| BROWSER-FACTORY | 2026-10-28 07:20 — 98ebac4 | Endurecimiento de proxy y perfiles; rotación y Chrome real pendientes | P0 — seguridad operativa |
| PANEL-DE-CONTROL | 2026-10-22 16:54 — 71b46ef | Corrige exclusión de local-sim.ts por gitignore | P0 — operación/deployment |
| INSTALADOR | 2026-10-22 19:44 — 2313afe | Runtime, fuentes GitHub, confianza Ed25519 y contrato EEC; Windows/VPS/CA con límites explícitos | P0 — deployment y confianza |
| YOUTUBE-AUTOMATION | 2026-10-28 07:34 — 2589e4e | Gate de producción piloto NotebookLM | P1 — ruta a ingresos/contenido |
| AGENTE-LEADS | 2026-10-27 00:32 — e1c0d5f | Catálogo JVZoo, deep links y cierre de ventas | P1 — monetización |
| AGENTE-KREA | 2026-10-08 01:50 — 460605e | Verificación condicional; 241 tests declarados, proveedores externos no verificados | P1 — producción de contenido |
| NEX-SCOPE | 2026-10-27 00:35 — 43e2e4c | Lote nocturno de ofertas JVZoo hacia fábrica de video | P1 — investigación y pipeline |
| AGENTE-CHISMOSO | 2026-10-27 00:26 — e0e39b8 | Puente de oportunidades hacia YouTube y NEX-SCOPE | P1 — descubrimiento de demanda |
| Extension-CAZADOR | 2026-10-26 20:30 — 4fc7de5 | Extractor JVZoo y páginas de ventas | P1 — captación de ofertas |
| COMUNITI-MANAGER | 2026-10-28 02:38 — 4bbdc7b | Gestión de comunidad y mejoras del panel; tests E2E reportados en commit | P1 — operación de canales |
| CONTADOR-PUBLICO | 2026-10-27 10:20 — 5102914 | Funciones tributarias colombianas e ingesta PDF/XLSX/CSV; 36 tests declarados | P1 — producto vertical |
| ERP-ADMON | 2026-10-22 19:56 — 606d30e | Ajustes UI/cache; cero errores runtime declarados en commit | P1 — administración |
| RADAR-SECOP2 | 2026-10-22 06:56 — 9f6a0d5 | Worklog declara CI verde en ese commit | P1 — oportunidades públicas |
| AGENTE-TRADING | 2026-10-19 03:01 — 0559ac3 | Pine v2.2 y alertas JSON; sin inferir desempeño financiero | P2 — aislado, no priorizar despliegue |
| PURO-PODER-CONOCIMIENTO | 2026-10-07 04:53 — 9f1704b | Último commit “Add files via upload”; propósito/madurez por confirmar | P2 — descubrimiento |
| MAXI-APP | 2026-04-13 19:37 — 24f493b | Enlace de privacidad; repositorio notablemente menos reciente | P2 — descubrir estado/propósito |
| CONTEXT | 2026-10-28 07:20 — 8584f46 | Documentación de auditoría de Browser Factory | Transversal — fuente canónica de decisiones |

## Orden de organización
### Fase 1 — Aclarar mapa y contratos (ahora; solo lectura/documentación)
- Leer README, manifiestos y documentos de integración de cada repositorio.
- Identificar dueño funcional, entradas/salidas, dependencias y estado de evidencia.
- Consolidar contratos CRM-ALBRA → WHATSPOWER → WHATSPOWER-ULTRA y verificar la descripción de integraciones en CHAT-X.
- Registrar preguntas abiertas; no correr pruebas ni activar servicios.

### Fase 2 — Cerrar el inventario documental
- Confirmar README/propósito, rama predeterminada, commit, CI reciente y dependencias para los 23 repos.
- Marcar cada afirmación como documentada, implementada, prueba reportada, prueba reproducida o producción validada.
- Identificar repositorios sin actividad reciente y documentación faltante.

### Fase 3 — Preparar plan de validación (sin ejecutarlo todavía)
- Casos de prueba negativos de autenticación/autorización para APIs internas.
- Contrato de bajas/supresiones y locks humanos a través de las tres capas.
- Idempotencia, firma HMAC/timestamp y prevención de despacho duplicado.
- Revisión de CI posterior a cambios y gates para cualquier prueba con tráfico real.
- Ejecutar solo después de autorización explícita.

### Fase 4 — Priorizar ingresos
- Ruta A: CRM + WhatsPower + Ultra + Agente Leads + Comuniti Manager.
- Ruta B: NEX-SCOPE + Extension-CAZADOR + Agente Chismoso + YouTube Automation + KREA + Browser Factory.
- Ruta C: Contador Público + ERP-ADMON.
- CHAT-X se considera interfaz de orquestación, no prueba de que las integraciones estén operativas.
- Mantener Trading aislado y revisar propósito de MAXI-APP y PURO-PODER-CONOCIMIENTO antes de asignar esfuerzo.

## Hallazgos / límites vigentes
- En el código revisado de WHATSPOWER hay manejadores de flujos, consultas internas de locks/supresiones y liberación de locks sin autenticación visible. El webhook usa HMAC, pero la validación de timestamp es opcional y la comparación de firma es igualdad directa. Revisar antes de cualquier exposición de red.
- En WHATSPOWER-ULTRA, el último commit documenta fail-closed del secreto interno en el gate de piloto; la revisión previa encontró rutas de API de campaña sin autenticación visible en sus manejadores. Confirmar el archivo y el despliegue actual antes de sacar conclusión de exposición.
- BROWSER-FACTORY: la rotación/revocación de credenciales históricas y validación con Chrome/proxy real siguen pendientes según el commit de seguridad.
- DESPACHO-JURIDICO: fix reciente menciona fallos de CI anteriores; el CI posterior no se debe dar por verde sin evidencia.
- No se han ejecutado pruebas en esta fase ni se han enviado mensajes/campañas.

## Último commit por repositorio
- MAXI-APP: https://github.com/ALBRA8/MAXI-APP/commit/24f493b63ddb83ceb3c23233135228d139b27598
- YOUTUBE-AUTOMATION: https://github.com/ALBRA8/YOUTUBE-AUTOMATION/commit/2589e4ebf6103943d7a0ef5ef2d65716ffa1d5ed
- CRM-ALBRA: https://github.com/ALBRA8/CRM-ALBRA/commit/ea87cc279aa04ca2fd0bfac56b1b06b307087830
- AGENTE-LEADS: https://github.com/ALBRA8/AGENTE-LEADS/commit/e1c0d5f1e97fb5d7578c11cd08fbe23d9b6149a9
- AGENTE-TRADING: https://github.com/ALBRA8/AGENTE-TRADING/commit/0559ac3975149d2ac6b4e0ea0080e18635f4b2f8
- NEX-SCOPE: https://github.com/ALBRA8/NEX-SCOPE/commit/43e2e4c03610ff5495bb29a6fe3a7ea7736f8211
- RADAR-SECOP2: https://github.com/ALBRA8/RADAR-SECOP2/commit/9f6a0d549cc31588cff621baa089fae62cde4692
- AGENTE-CHISMOSO: https://github.com/ALBRA8/AGENTE-CHISMOSO/commit/e0e39b8e61d3cc68fbdc1781455862fdc858db12
- AGENTE-KREA: https://github.com/ALBRA8/AGENTE-KREA/commit/460605e5b4966e5e9243c1ec3fcb4852c6758e1b
- Extension-CAZADOR: https://github.com/ALBRA8/Extension-CAZADOR/commit/4fc7de5d8a6c0b87826e94085a6ecb0310938a82
- PURO-PODER-CONOCIMIENTO: https://github.com/ALBRA8/PURO-PODER-CONOCIMIENTO/commit/9f1704b9ce8b3c992e48d0cb044b105d2e3e8904
- DESPACHO-JURIDICO: https://github.com/ALBRA8/DESPACHO-JURIDICO/commit/392f41699dec29a978ec9d338b0ff104502aa0e3
- AGENTE-GENERAL: https://github.com/ALBRA8/AGENTE-GENERAL/commit/69c79dad8337be156bcc60e74b53f98d5a993d54
- CONTEXT: https://github.com/ALBRA8/CONTEXT/commit/8584f467c7c9d0c5a8b255d9b3fad16b2f9a3e04
- ERP-ADMON: https://github.com/ALBRA8/ERP-ADMON/commit/606d30e3c00ad3825bacfc81bdbc2a430dd82894
- PANEL-DE-CONTROL: https://github.com/ALBRA8/PANEL-DE-CONTROL/commit/71b46ef5a58795aa032474298829befa93031020
- INSTALADOR: https://github.com/ALBRA8/INSTALADOR/commit/2313afea75452554b79d305bafd7091e77db3e00
- WHATSPOWER: https://github.com/ALBRA8/WHATSPOWER/commit/f4f7fc9f18d92ab1c1a86e57ee58cadb18105546
- WHATSPOWER-ULTRA: https://github.com/ALBRA8/WHATSPOWER-ULTRA/commit/e6ef56d6e0f4fc379078447f347dcef74bc711d7
- COMUNITI-MANAGER: https://github.com/ALBRA8/COMUNITI-MANAGER/commit/4bbdc7b95b837527bf8dae5920675b54f49bb5eb
- CONTADOR-PUBLICO: https://github.com/ALBRA8/CONTADOR-PUBLICO/commit/51029146d891bec12b534c3da95c8f089116cff3
- CHAT-X: https://github.com/ALBRA8/CHAT-X/commit/fa2fffccdfd0c9bc247638dddd0885baff140cf1
- BROWSER-FACTORY: https://github.com/ALBRA8/BROWSER-FACTORY/commit/98ebac48be8e07a7497949a09f75aeea12c01e1a
