## [0.6.16] - 2026-10-26 — Auditoría estática de DESPACHO-JURIDICO

### Added / Updated
- `03_AUDITS/DESPACHO_JURIDICO_REPOSITORY_AUDIT_2026-10-26.md`: revisión de `ALBRA8/DESPACHO-JURIDICO` `main` @ `277be56540dd7298f3ebc6a5654c2c7aaa1ba0c5`.
- Actualizadas las fichas de agente/proyecto, `CURRENT_STATE.md`, `KNOWLEDGE_INDEX.md` y el índice de auditorías.
- Se registran dos bloqueos P0: workflow CI aparentemente inválido (causa exacta no confirmada por log) y falta de prueba de que el hash del corpus V1.1 corresponda a bytes originales del HTML oficial.

### Evidence / Limits
- Inspección estática/documental y consulta de GitHub Actions; no se ejecutaron localmente tests, lint o clean-room, ni se verificó cada documento jurídico contra su fuente primaria.
- No se modificó `ALBRA8/DESPACHO-JURIDICO`; solo se escribieron documentos en `ALBRA8/CONTEXT`.
- El dictamen es PARTIAL / NOT VERIFIED INDEPENDENTLY BY TEST EXECUTION; no es certificación de producción ni dictamen jurídico sobre el contenido de cada norma.

---

## [0.6.15] - 2026-10-26 — Revalidación de CRM-ALBRA, WHATSPOWER y WHATSPOWER-ULTRA

### Added / Updated
- Nuevo informe `04_PROJECTS/CRM_WHATSPOWER_WHATSPOWER_ULTRA_REVALIDATION_2026-10-26.md`.
- `CURRENT_STATE.md` y `KNOWLEDGE_INDEX.md` actualizados para reflejar la revalidación posterior del día.

### Evidence / Limits
- HEADs verificados de nuevo: CRM-ALBRA `db95a5b`, WHATSPOWER `d854749`, WHATSPOWER-ULTRA `b7b5a5d`.
- Lectura estática focalizada confirma hallazgos de autorización en rutas de locks/campañas/webhooks y el fallback/condicionalidad de HMAC. El alcance no es pentest.
- `dispatch-gate.mjs` no está accesible en CRM `main`; D.5/E.2.R local no se acredita en esa rama.
- No se ejecutaron tests/builds, no se modificaron repositorios de producto y no se hicieron envíos reales.

---

## [0.6.14] - 2026-10-26 — Actualización cruzada CRM-ALBRA, WHATSPOWER y WHATSPOWER-ULTRA

### Added / Updated
- `04_PROJECTS/CRM_WHATSPOWER_WHATSPOWER_ULTRA_REPOSITORY_REFRESH_2026-10-26.md`: HEAD públicos, cambios recientes, arquitectura por capas, deuda documental, hallazgos estáticos y gates de seguridad.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` y `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` actualizados con la revisión.

### Evidence / Safety
- HEADs consultados: CRM-ALBRA `db95a5b` (2026-10-22), WHATSPOWER `d854749` y WHATSPOWER-ULTRA `b7b5a5d` (2026-10-26).
- `dispatch-gate.mjs` no encontrado en CRM-ALBRA `main`; los informes de estado local D.5/E.2.R no prueban integración a esa rama.
- Los nuevos tests declarados por los repositorios no se reejecutaron. Pagos reales, Meta Cloud, integración E2E y despliegue externo no se certifican.
- Hallazgos estáticos prioritarios: autenticación y HMAC en WHATSPOWER, autorización de locks, endpoints administrativos, webhook/tenant/privacidad en ULTRA, y conciliación de la ficha histórica.
- No se modificaron los repositorios de producto ni se enviaron mensajes; los gates de despacho real permanecen cerrados.

---

## [0.6.13] - 2026-10-25 — Registro del protocolo CRM-ALBRA E.2.R.6

### Added / Updated
- `03_AUDITS/CRM_ALBRA_E2R6_SUPERVISED_VALIDATION_PROTOCOL_2026-10-25.md`: protocolo fuente para una futura validación operativa supervisada, matriz hermética, protección SQLite/WAL, comprobación de runtime y gates de autorización.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` y `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` enlazan el registro y preservan la separación entre código/pruebas reportados y runtime no verificado.

### Evidence / Limits
- `READY FOR AUTHORIZATION REVIEW` es el dictamen del informe fuente; 35/35 pruebas son resultados reportados, no reejecutados independientemente al documentar.
- Se añadió una advertencia crítica: no ejecutar la sonda propuesta con `senderType: "sonda_invalida"` contra el daemon operativo mientras la versión antigua pueda seguir cargada, porque podría caer en la rama manual y alcanzar el transporte.
- Runtime `NOT VERIFIED`; envío real `NOT VERIFIED` y no autorizado; E.3 no autorizada.
- Solo se modificó documentación en `ALBRA8/CONTEXT`. No se reiniciaron procesos, no se consultó ni modificó la base operativa y no se enviaron peticiones a CRM-ALBRA.

---

## [0.6.12] - 2026-10-25 — Registro de informe fuente de auditoría D.5

### Added / Updated
- `04_PROJECTS/CRM_ALBRA_D5_FINAL_AUDIT_2026-10-25.md`: registra el informe de auditoría D.5 aportado por el propietario, con matriz HTTP, análisis de crash, pruebas declaradas, línea base reportada y limitaciones.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` y `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` enlazan el nuevo registro.

### Evidence / Limits
- La clasificación `VERIFIED WITH LIMITATIONS` es la conclusión del informe fuente, no una certificación independiente realizada al registrar el documento.
- `PrismaClient` real y entrega E2E a WhatsApp siguen `NOT VERIFIED`; recuperación automática de huérfanos/resultados ambiguos pendiente.
- Se omitieron números telefónicos/datos personales directos de la salida operativa por tratarse de un repositorio público.
- No se modificó CRM-ALBRA ni se consultó su base operativa durante esta actualización documental.

---

## [0.6.11] - 2026-10-24 — Primer piloto real saliente de Capa 2B reportado

### Added / Updated
- Nuevo cierre documental `04_PROJECTS/CRM_ALBRA_2B_FIRST_REAL_OUTBOUND_PILOT_2026-10-24.md`.
- `CURRENT_STATE.md` y `KNOWLEDGE_INDEX.md` actualizados con el resultado reportado y el siguiente hito.
- Arquitectura CRM + Chat Marketing actualizada para distinguir MVP 2B, primer envío saliente reportado y validación pendiente de respuesta entrante.

### Evidence / Limits
- El informe de Antigravity/propietario reporta un envío único de `573163301286` (negocio) a `573017543786` (cliente), texto “Tengo estos zapatos para la venta.”, HTTP 200, Message ID y persistencia `outbound`.
- JIT lock/suppression, restauración de `allowRealDispatch=false` y preservación de `AGENTIC_2A_ENABLED=false` son resultados reportados, no inspeccionados independientemente en esta actualización.
- El despacho registrado no demuestra por sí solo entrega o lectura; no se autorizan campañas adicionales. El próximo hito es observar una respuesta entrante natural sin enviar mensajes para forzarla.
- Solo se modificó documentación en `ALBRA8/CONTEXT`; no se modificaron CRM-ALBRA ni clones locales.

---

## [0.6.10] - 2026-10-24 — MVP de Capa 2B construido; integración pendiente

### Added / Updated
- Nuevo documento `04_PROJECTS/CRM_ALBRA_2B_MVP_ACCEPTANCE_AND_INTEGRATION_GATE_2026-10-24.md`.
- `CURRENT_STATE.md`, `KNOWLEDGE_INDEX.md` y la arquitectura CRM + Chat Marketing se actualizan para reflejar el MVP 2B reportado construido y la suite CP-B01–CP-B10 reportada 10/10 PASS.
- El siguiente hito es integración contractual controlada sin envío real: daemon CRM, locks/supresiones de 2A y reconciliación de resultados de despacho inciertos.

### Evidence / Limits
- Suite y arquitectura son resultados reportados por el propietario; no se reejecutaron independientemente en esta actualización.
- El informe indica `MockDispatcher`, sala limpia y cero mensajes reales. `AGENTIC_2A_ENABLED=false` se reporta preservado.
- No se modificaron repositorios de producto ni clones locales. No se conectaron servicios reales ni se autorizaron campañas o envíos de producción.
- La idempotencia local no se presenta como garantía distribuida de entrega exactamente una vez.

---

## [0.6.9] - 2026-10-24 — Cierre reportado de Capa 2A y autorización de especificación 2B

### Added / Updated
- Nuevo documento `04_PROJECTS/CRM_ALBRA_2A_PILOT_CLOSURE_AND_2B_SPEC_2026-10-24.md` con el resultado del piloto reportado, estado de seguridad, límites arquitectónicos y gate de implementación.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` actualizado con el cierre reportado de 2A y la autorización de redactar `FICHA.md` para 2B.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` actualizado para localizar el documento canónico.
- `04_PROJECTS/CRM_ALBRA_CHAT_MARKETING_LAYER_ARCHITECTURE_2026-10-24.md` actualizado para reflejar que el siguiente paso es la especificación 2B, no una nueva auditoría general.
- Se fijan los requisitos de lock check inicial/final, fallo cerrado ante estado desconocido, despacho exclusivo por CRM, no acceso directo a su BD comercial, supresiones/consentimiento e idempotencia.

### Evidence / Limits
- Piloto real, latencia de 321 ms, ausencia de duplicados, no invocación del LLM y reversión de `AGENTIC_2A_ENABLED=false`: **reportados por el propietario**, no reproducidos independientemente en esta actualización.
- `crmSynced: false` queda registrado como observación con explicación aportada; no se declara resuelto mediante inspección independiente del código.
- No se modificaron repositorios de producto ni clones locales. No se escribió código 2B, no se conectaron servicios y no se enviaron mensajes/campañas.

---

## [0.6.8] - 2026-10-24 — Arquitectura CRM ALBRA + Chat Marketing por capas

### Added / Updated
- Nuevo documento `04_PROJECTS/CRM_ALBRA_CHAT_MARKETING_LAYER_ARCHITECTURE_2026-10-24.md`.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` actualizado con la decisión conceptual de separar la ampliación en Capa 2A Agéntico/Conversacional y Capa 2B Superpowers/Chat Marketing.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` actualizado para localizar el documento canónico.
- Se fija CRM ALBRA como fuente comercial canónica; 2A/2B deben integrarse mediante contratos explícitos sin duplicar CRM.
- Se registra que la fase vigente es investigación/clasificación/diseño de contratos, no construcción.

### Evidence / Limits
- Documentación basada en la decisión e instrucciones registradas en la conversación del 2026-10-24.
- Los informes y rutas locales de ClonaKit/Antigravity se registran como referencias reportadas; no fueron inspeccionados desde esta escritura documental.
- No se modificó `ALBRA8/CRM-ALBRA` ni ningún clon de producto. No se implementaron contratos, endpoints ni funcionalidades; no se ejecutaron pruebas ni campañas.

---

## [0.6.7] - 2026-10-22 — Auditoría de INSTALADOR global + EEC + AGENTE-GENERAL

### Added / Updated
- Nuevo informe `03_AUDITS/INSTALLER_EEC_AGENT_GENERAL_INTEGRATION_AUDIT_2026-10-22.md` con inspección directa de los tres repositorios y sus HEAD consultados.
- `CURRENT_STATE.md` actualizado con el estado vigente de INSTALADOR, EEC/PANEL-DE-CONTROL y AGENTE-GENERAL.
- `KNOWLEDGE_INDEX.md` actualizado para localizar el informe y fijar las fronteras arquitectónicas.
- `EEC_MISSION_CONTROL.md` actualizado: existe implementación de EEC en repo, pero no certificación de producción ni integración de agentes reales.
- Dictamen: INSTALADOR ↔ EEC todavía no tiene integración E2E demostrada; el adapter VPS declara `isImplemented() === false`; los providers EEC por defecto son de simulación; el runtime del instalador aún no demuestra procesos reales.

### Evidence / Limits
- HEAD consultados: INSTALADOR `451d7203cfa9a9ba86026f058130b8c9d11c08ad`; PANEL-DE-CONTROL `71b46ef5a58795aa032474298829befa93031020`; AGENTE-GENERAL `69c79dad8337be156bcc60e74b53f98d5a993d54`.
- Inspección documental/código de contratos, adapters, runtime, pipeline y rutas API; no se ejecutaron suites ni despliegues. Los tests descritos por los commits no se consideran verificados de forma independiente.
- Solo se modificó documentación en CONTEXT; no se cambió código de los repositorios de producto.

---

# Changelog

## [0.6.6] - 2026-10-21 — Auditoría actual de AGENTE-KREA V2.1

### Added / Updated
- Nuevo informe `03_AUDITS/AGENTE_KREA_CURRENT_AUDIT_2026-10-21.md`.
- Se documenta KREA V2.1 como Product Factory comercial con oportunidad, decisión, dossier, blueprint, packaging, factory orchestration, assets/versioning, QA, readiness, launch package, content y feedback.
- Se separan los tests reportados por commits de la verificación independiente.
- Se registran gates: `npm run test:e2e` no ejecuta E2E y termina con código 0; `db:push` usa `--accept-data-loss`; revisar acceso a Doctor; PDF/QA real y proveedores/handoff permanecen sin certificación.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` actualizado para localizar la auditoría.

### Evidence / Limits
- Inspección documental directa de `package.json`, `prisma/schema.prisma`, `src/app/api/doctor/route.ts`, `vitest.config.ts`, `VERIFICATION-REPORT.md` e historial de commits.
- HEAD consultado: `460605e5b4966e5e9243c1ec3fcb4852c6758e1b` (2026-10-08).
- Sin ejecución clean-room, sin rerun de tests, sin despliegue y sin modificación de `ALBRA8/AGENTE-KREA`.
- La aprobación documentada sigue siendo condicional; proveedores externos `NOT_VERIFIED`.

## [0.6.4] - 2026-10-20 — Comparativa SRZburner × CAZADOR × NEX-SCOPE

### Added / Updated
- Se documenta la comparativa arquitectónica en `04_PROJECTS/SRZBURNER_CAZADOR_NEX_SCOPE_INTEGRATION_REVIEW.md`.
- Se confirma la recomendación de NEX-SCOPE como propietario funcional de inteligencia social; CAZADOR queda como extractor web general opcional, no como dependencia obligatoria.
- Se precisa una discrepancia de alcance: Chrome Web Store describe Instagram/TikTok; Chrome-Stats también menciona Facebook. Facebook queda pendiente de confirmación oficial.
- Se explicita que el endpoint MCP de NEX-SCOPE exige sesión autenticada; el acceso servicio-a-servicio requiere diseño propio y revisión de seguridad.

### Evidence / Limits
- Inspección de README, manifest y extractor de CAZADOR; README, registro de herramientas, ruta MCP y contrato de procedencia de NEX-SCOPE.
- Sin ejecución runtime, sin instalación de extensiones, sin conexión a SRZ Club y sin integración implementada.
- No se modificaron repositorios de producto. La documentación no certifica compatibilidad ni aprobación legal/seguridad.


## [0.6.3] - 2026-10-20 — Evaluación SRZburner × NEX-SCOPE

### Added / Updated
- Nuevo documento `04_PROJECTS/SRZBURNER_NEX_SCOPE_INTEGRATION.md`: evaluación de SRZburner como fuente potencial de inteligencia multiplataforma para NEX-SCOPE.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`: indexación del documento.
- Recomendación provisional: NEX-SCOPE es el candidato natural por su agente, registro de herramientas, endpoint MCP y contrato de procedencia.

### Evidence / Limits
- La ficha pública indexada describe investigación/descarga/biblioteca/estadísticas para Instagram, TikTok y Facebook y exige cuenta SRZ Club.
- No se verificó API pública, SDK, webhook, exportación soportada ni código fuente de SRZburner; la viabilidad técnica sigue pendiente.
- No se modificó `ALBRA8/NEX-SCOPE`, no se implementó integración y no se autorizó uso de la extensión. Solo se actualizó `ALBRA8/CONTEXT`.
- Gates siguientes: confirmar interfaz oficial y términos; diseñar contrato read-only; revisar seguridad antes de cualquier prototipo.


## [0.6.2] - 2026-10-19 — YOUTUBE-AUTOMATION Flow Script Processor LIVE FAIL

### Added / Updated
- Nuevo registro documental `03_AUDITS/YOUTUBE_AUTOMATION_FLOW_SCRIPT_PROCESSOR_POST_LIVE_FAIL_2026-10-19.md`: ejecución LIVE real, fallo de extracción de metadatos, autopsia de `fetch(Request)`, fix local reportado y checklist de auditoría independiente.
- `00_MASTER_CONTEXT/CURRENT_STATE.md`: estado de YOUTUBE-AUTOMATION actualizado, con distinción entre reporte del implementador y verificación independiente.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`: indexación del nuevo registro.
- `04_PROJECTS/YOUTUBE_AUTOMATION_CLOSURE.md`: estado específico del parser/telemetría y gates previos a commit/LIVE.

### Evidence / Limits
- Flow completó el video, pero la extensión no capturó metadatos esperados; `LIVE FAIL` aplica al parsing/telemetría.
- Antigravity reportó fix local y 235/235 tests, no reejecutados independientemente aquí.
- El diff íntegro aún no ha sido auditado en esta operación. No existe LIVE PASS del fix.
- Sin cambios a `ALBRA8/YOUTUBE-AUTOMATION`; la escritura se limitó a `ALBRA8/CONTEXT`.
- Gates: NO COMMIT, NO LIVE, NO MERGE, NO CONSTRUCTOR.


Los cambios relevantes de ALBRA-CONTEXT se registran aquí en orden cronológico inverso. Este archivo resume cambios documentales; el historial Git conserva los commits completos.

## [0.6.0] - 2026-10-14 — Radar de Clientes: P2 cerrado (D-0015)

### Added / Updated
- **P2 CERRADO Y VALIDADO** (commit 5ec3cc0, rama `radar-clientes/p0-security`):
  - PageSpeed Insights v5 (contrato 5): módulo `pagespeed.ts` con estados explícitos OK/UNREACHABLE/TIMEOUT/INVALID_URL, `parseScores`/`normalizeTargetUrl` puras, timeout 25s inyectable, key opcional (`Integration.pageSpeedApiKey`, cuota anónima gratuita sin key), `redactSecret` anti-fuga de key en errores, resultados persistidos con url evaluada + auditedAt + keyUsed. Integrado en el pipeline para calificados TIER_A/B con website (batches de 2, tope 10 por job).
  - Determinización de pain/tier ANTES del LLM: núcleo puro `qualification-deterministic.ts` con 13 reglas auditables (`basis`), descalificaciones (sin presencia digital, CLOSED_PERMANENTLY, franquicia), nicheFit por keyword match documentado, tier mapping determinista; el LLM queda reducido a NARRATIVA (nunca modifica tier/pain/nicheFit); fallback determinista sin IA.
  - Taxonomía FACT/INFERENCE/HYPOTHESIS/RECOMMENDATION en intelligence: normalización estricta de kinds (sinónimos es/en, irreconocibles descartados), builder determinista de items (nunca genera HYPOTHESIS), cap 10 + dedupe, persistida en `Prospect.intelligenceItems`.
  - Migración aditiva `20261013211916_p2_pagespeed_intelligence` (3 ADD COLUMN, sin DROP/DELETE, verificada columna a columna).
  - UI: PageSpeed con scores por umbral + estados ES (error crudo nunca renderizado) + chips de taxonomía en ficha.
- Evidencia: tsc 0 errores, 377/377 tests (14 archivos, +103), lint 0 errores, build OK, push remoto verificado (5ec3cc0).

### Notes
- Pendiente autorizado en plan: P3 (experiencia Radar de Clientes — CSV real, ficha imprimible/PDF, borradores de primer contacto, Radar de Nichos, GET /api/prospects/[id], glosario ES).
- Sin despliegues, sin envíos reales, sin migraciones destructivas; PSI es gratuita (cuota anónima o key propia del usuario).

## [0.5.0] - 2026-10-14 — Radar de Clientes: P0 cerrado + P1 cerrado (D-0015)

### Added / Updated
- Ejecución autorizada por propietario (D-0015) sobre `ALBRA8/AGENTE-LEADS`, rama `radar-clientes/p0-security`.
- **P0 CERRADO Y VALIDADO** (commits 7125bb7 + e956263): seed-demo gated sin exposición de credenciales, signup role:user, scoring comercial real con breakdown persistido, extracción completa de teléfono/email, 22 errores TS corregidos + `ignoreBuildErrors` eliminado, módulo SSRF para fetchers server-side, vitest+eslint operativos. Evidencia: tsc 0 errores, 182/182 tests, lint 0 errores, build OK.
- **P1 CERRADO Y VALIDADO** (commit 94372d9): envelope de proveedores con taxonomía AUTH/UNAVAILABLE/RATE_LIMIT/TIMEOUT/EMPTY/INVALID_INPUT/UNKNOWN (contrato 7), cadena de fallback `searchWithFallback`, Google Places completo — place_id/coords/rating/ratingCount/businessStatus/paginación nextPageToken (máx 3 páginas)/keywords en textQuery/timeout 15s/clasificación de errores (contrato 1), dedup multi-señal EXACT/STRONG/PROBABLE, tabla `Evidence` por campo con confianza y fuente (contrato 2), clasificación de presencia digital en 4 estados (contrato 3), marcado estructural DEMO (`isMock`, sourceUrl null, bloqueo de propuestas 403 sobre mocks — contratos 10), trazabilidad de coste/llamadas por proveedor y SearchJob con estado real completed|partial|failed + fuentes fallidas (contrato 6), migración aditiva `20261013203215_p1_places_evidence_digital_presence` (sin DROP/DELETE, verificada), UI con badges DEMO/presencia digital/rating/businessStatus.
- Evidencia P1: tsc 0 errores, 274/274 tests (10 archivos), lint 0 errores, build producción OK, push remoto verificado (94372d9).

### Notes
- Pendientes autorizados en plan: P2 (inteligencia comercial — PageSpeed v5, taxonomía FACT/INFERENCE/HYPOTHESIS/RECOMMENDATION) y P3 (experiencia — CSV/PDF/borradores/Radar de Nichos).
- Sin despliegues a producción, sin envíos reales, sin migraciones destructivas, sin costes de API no autorizados.
## [0.4.0] - 2026-10-12 — Wave 4 / Closure

### Added / Updated
- Cierre operacional de CONTEXT.
- `ORIGINAL_SOURCE_CATALOG.md` con los cuatro originales principales de Library y sus file IDs.
- `FINAL_REPOSITORY_STATUS_MATRIX.md` con último commit conocido y nivel de evidencia/auditoría.
- Registros de cierre para EEC/Mission Control, Audiobook/Rights Research y YOUTUBE-AUTOMATION/HANDS.
- `WAVE4_CLOSURE_REPORT.md` y sesión Wave 4.
- `CURRENT_STATE.md`, `KNOWLEDGE_INDEX.md`, `ALBRA_BOOTSTRAP.md` y `MIGRATION_MISSING_INFORMATION.md` actualizados.

### Notes
- CONTEXT se declara `READY FOR OPERATIONAL USE`.
- La declaración significa completo en función de la información recuperable y trazable; no significa 100% de toda fuente histórica externa.
- Los cuatro prompt originals siguen disponibles en Library; su duplicación literal dentro de GitHub CONTEXT queda marcada `NOT_YET_MIGRATED` porque el canal de escritura disponible no permite transferir directamente archivos montados.
- Los dos ZIPs timestamped exactos siguen `MISSING_BINARY`.
- No se modificó ningún repositorio distinto de `ALBRA8/CONTEXT`.

## [0.3.0] - 2026-10-12 — Wave 3

### Added / Updated
- Recuperación dirigida de AGENTE-GENERAL, KREA, LEADS, CHISMOSO, YOUTUBE-AUTOMATION/HANDS, PURO-PODER-CONOCIMIENTO, Extension-CAZADOR y NEX-SCOPE.
- Recuperación adicional de evidencia para CRM ALBRA y Despacho Jurídico.
- `WAVE3_PROJECT_RECOVERY.md`, `WAVE3_RECOVERED_SOURCES.md`, `WAVE3_ARTIFACT_RECOVERY.md`, `WAVE3_RECOVERY_REPORT.md`, `WAVE3_FINAL_TRACE.md` y sesión Wave 3.
- `CURRENT_STATE.md`, `KNOWLEDGE_INDEX.md`, `AGENT_REGISTRY.md` y `MIGRATION_MISSING_INFORMATION.md` actualizados.
- Exactos originales de cuatro fuentes históricas de prompts localizados en Library.
- Dos ZIPs timestamped exactos siguen marcados `MISSING_BINARY`; variantes anteriores disponibles quedan inventariadas.

### Notes
- Wave 3 no reinicia ni reemplaza Waves 1/2.
- No se modificó ningún repositorio distinto de `ALBRA8/CONTEXT`.
- La migración continuaba abierta en ese momento.

## [0.3.0] - 2026-10-13

### Added
- `03_AUDITS/AGENTE_LEADS_RADAR_AUDIT_2026-10-13.md`: auditoría de solo lectura de `ALBRA8/AGENTE-LEADS` @ `e73d15b` con hallazgos P0/P1/P2-P3 y verificación reproducible.
- `04_PROJECTS/RADAR_DE_CLIENTES_PLAN.md`: plan autorizado P0→P3 con contratos compartidos y restricciones de ejecución.
- `02_DECISIONS/DECISION_LOG.md`: D-0015 (EXPLICIT USER DECISION) — autorización del propietario para modificar AGENTE-LEADS con condiciones.

## [0.2.0] - 2026-10-12

### Changed
- `ALBRA_BOOTSTRAP.md`: explicitó que solo `ALBRA8/CONTEXT` puede recibir cambios; los demás repositorios son de solo lectura. Actualizó el mapa de archivos base existentes y el orden de incorporación.

## [0.1.0] - 2026-10-12

### Added
- `ALBRA_BOOTSTRAP.md`, `README.md`, `SECURITY.md` y `CHANGELOG.md` iniciales.


## [0.6.1] - 2026-10-14

### Added
- `00_MASTER_CONTEXT/AGENTE_GENERAL_SECURITY_CLOSURE_PLAN.md`: contrato de controles de seguridad y puertas de aceptación para AGENTE-GENERAL; identifica requisitos, evidencia requerida, orden de integración y bloqueos para freeze.

### Security / Status
- `CURRENT_STATE.md` registra AGENTE-GENERAL como NOT READY FOR FINAL FREEZE / PRODUCTION NOT VERIFIED. La certificación CI de 648 tests pertenece al commit documentado y no acredita PRs abiertos ni despliegue real.
- No se modificó AGENTE-GENERAL. El plan es especificación y checklist, no afirmación de implementación ni certificación dinámica.


## [0.6.2] - 2026-10-19 — pasada de diferencias de repositorios

### Added
- `03_AUDITS/REPOSITORY_DELTA_REVIEW_2026-10-19.md`: commits recientes de los 14 repositorios accesibles; CI y ramas divergentes de AGENTE-GENERAL; cambios recientes de YOUTUBE-AUTOMATION, CRM-ALBRA y AGENTE-TRADING; límites de evidencia.

### Updated
- `00_MASTER_CONTEXT/CURRENT_STATE.md`: estado de repositorios posterior a Wave 4 y aclaración de CI versus estado real de producción.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`: referencia al nuevo informe.
- `03_AUDITS/FINAL_REPOSITORY_STATUS_MATRIX.md`: addendum con hashes/estados consultados en esta pasada, conservando la tabla original como histórico de Wave 4.
- `04_PROJECTS/ALBRA_BROWSER_FACTORY.md`: registra que el propietario declara implementación local en su PC; la implementación queda NO INSPECCIONADA INDEPENDIENTEMENTE hasta tener archivos/código disponibles. Se aclara que Browser Factory es el nombre del producto y Dolphin Anty solo referencia funcional.

### Evidence boundaries
- No se modificaron repositorios fuente distintos de CONTEXT.
- No se inspeccionó el PC local ni se ejecutaron todas las suites de los 14 repositorios.
- Los resultados de pruebas citados desde commits/documentación se conservan como evidencia reportada, no como reruns independientes.


## [0.6.6] - 2026-10-21 — Revisión actual de AGENTE-KREA

### Added / Updated
- Nueva ficha `04_PROJECTS/AGENTE_KREA_CURRENT_STATE_2026-10-21.md`: estado de KREA V2.1 como Product Factory comercial y sus fronteras con LEADS, CHISMOSO, Despacho Jurídico y YOUTUBE-AUTOMATION.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` e índice actualizados con la revisión del repo `ALBRA8/AGENTE-KREA`.
- Se preserva el límite entre los tests/Golden Flows reportados por el proyecto y una repetición independiente.

### Hallazgos relevantes
- `package.json` declara `test:e2e` como `echo ... && exit 0`; el script no ejecuta E2E automatizado.
- `db:push` usa `prisma db push --accept-data-loss`; riesgo operacional si se ejecuta sin revisar schema y respaldo.
- LLM real/Z.AI, dos comprobaciones de QA visual y entrega externa permanecen NOT_VERIFIED/no conectadas según el informe.
- HEAD visible consultado: `460605e5b4966e5e9243c1ec3fcb4852c6758e1b` (2026-10-08); no se encontró commit posterior en la consulta del 2026-10-21.

### Evidence / Limits
- Inspección documental de `package.json`, `KREA_FINAL_VERIFICATION.md` e historial de commits.
- No se ejecutó código, pruebas, proveedor LLM ni operaciones de base de datos; no se modificó AGENTE-KREA.


## [0.6.7] - 2026-10-21 — Reauditoría cruzada NEX-SCOPE + AGENTE-KREA

### Added / Updated
- `06_SESSIONS/2026-10-21_NEX_SCOPE_KREA_REAUDIT.md`: registro de continuidad y fronteras entre proyectos.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` y `00_MASTER_CONTEXT/CURRENT_STATE.md` actualizados con referencias canónicas.
- Se aclara que `03_AUDITS/AGENTE_KREA_CURRENT_AUDIT_2026-10-21.md` es la auditoría principal de KREA y que la ficha en `04_PROJECTS/` es complementaria.

### Findings retained
- NEX-SCOPE tiene una arquitectura agentic/MCP/memory/provenance/trace más madura que su descripción anterior, pero eso no prueba captura social nativa ni integración autorizada con SRZburner.
- KREA V2.1 es una Product Factory comercial; `test:e2e` no corre E2E y `db:push --accept-data-loss` requiere precaución.
- Las pruebas/Golden Flows reportados por KREA no se reejecutaron independientemente.
- No se modificaron NEX-SCOPE, KREA ni CAZADOR; únicamente se actualizó CONTEXT.

## [0.6.8] - 2026-10-24 — CRM ALBRA: hito C2B inbound

### Added / Updated
- `04_PROJECTS/CRM_ALBRA_2B_INBOUND_RESPONSE_HITO_C2B_2026-10-24.md`: registra el mensaje entrante natural “Hola”, la asociación a contacto/conversación y la deduplicación, según el informe de Antigravity.
- `00_MASTER_CONTEXT/CURRENT_STATE.md`, `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` y `04_PROJECTS/CRM_ALBRA_CHAT_MARKETING_LAYER_ARCHITECTURE_2026-10-24.md` actualizados con el nuevo hito y sus límites de evidencia.

### Safety / Evidence
- Se conserva `isAutoReply=0` para la conversación piloto; `AGENTIC_2A_ENABLED=false` y `allowRealDispatch=false` se reportan preservados.
- No se autorizaron respuestas automáticas ni envíos adicionales.
- El resultado se clasifica como reportado por Antigravity; la DB y los logs locales no se revalidaron independientemente durante esta actualización.

## [0.6.9] - 2026-10-26 — CRM-ALBRA: baseline Prisma y preparación standalone

### Added / Updated
- `03_AUDITS/CRM_ALBRA_PRISMA_BASELINE_AND_STANDALONE_READINESS_2026-10-26.md`: registra la marcación reportada de `0_init`, el estado standalone local y las limitaciones del dictamen.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` y `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` actualizados con el estado `READY FOR PRODUCTION REVIEW`.

### Evidence / Safety
- El comando de baseline, el estado Prisma, la integridad SQLite y la salud de los servicios se registran como resultados del informe del propietario/Antigravity; no se reprodujeron independientemente en este cambio documental.
- No se certifica despliegue externo al 100 %. `ALLOW_REAL_DISPATCH=false` y `isAutoReply=0` permanecen reportados como vigentes; no se autorizaron nuevos despachos reales.
- Solo se modificó `ALBRA8/CONTEXT`; no se modificó el repositorio CRM-ALBRA.
