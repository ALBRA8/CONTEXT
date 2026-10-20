# Changelog

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
