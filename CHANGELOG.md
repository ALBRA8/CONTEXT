# Changelog

Los cambios relevantes de ALBRA-CONTEXT se registran aquí en orden cronológico inverso. Este archivo resume cambios documentales; el historial Git conserva los commits completos.

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
