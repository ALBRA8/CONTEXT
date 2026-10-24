# Índice de conocimiento

- Estado: CLOSURE / WAVE 5
- Versión: 0.6.9
- Base: `99c95b20833ba4d6696dbc7fc5e66175e266721e`

## Fuentes canónicas internas

| Tema | Fuente | Estado |
|---|---|---|
| Entrada universal | [ALBRA_BOOTSTRAP.md](../ALBRA_BOOTSTRAP.md) | CLOSURE |
| Seguridad | [SECURITY.md](../SECURITY.md) | PROPOSED |
| ADN | [ALBRA_DNA.md](ALBRA_DNA.md) | PROPOSED |
| Arquitectura | [ALBRA_ARCHITECTURE.md](ALBRA_ARCHITECTURE.md) | PROPOSED |
| Protocolo | [CONTEXT_PROTOCOL.md](CONTEXT_PROTOCOL.md) | PROPOSED |
| Estado vivo | [CURRENT_STATE.md](CURRENT_STATE.md) | CLOSURE |
| Gaps de migración | [MIGRATION_MISSING_INFORMATION.md](MIGRATION_MISSING_INFORMATION.md) | CLOSURE |
| Registro de agentes | [../01_AGENTS/AGENT_REGISTRY.md](../01_AGENTS/AGENT_REGISTRY.md) | Wave 3 evidence |
| Decisiones | [../02_DECISIONS/DECISION_LOG.md](../02_DECISIONS/DECISION_LOG.md) | Historical + Wave 2 |
| Auditorías | [../03_AUDITS/FINAL_REPOSITORY_STATUS_MATRIX.md](../03_AUDITS/FINAL_REPOSITORY_STATUS_MATRIX.md) | CLOSURE |
| Historial de auditorías | [../03_AUDITS/WAVE2_AUDIT_HISTORY.md](../03_AUDITS/WAVE2_AUDIT_HISTORY.md) | Historical |
| Proyectos | [../04_PROJECTS/README.md](../04_PROJECTS/README.md) + project closure docs | CLOSURE / WAVE 5 |
| Prompts/frameworks | [../05_PROMPTS/ORIGINAL_SOURCE_CATALOG.md](../05_PROMPTS/ORIGINAL_SOURCE_CATALOG.md) + operational framework references | SOURCE-PRESERVED / WAVE 5 |
| Sesiones | [../06_SESSIONS/2026-10-12_MIGRACION_WAVE4.md](../06_SESSIONS/2026-10-12_MIGRACION_WAVE4.md) | CLOSURE |
| Artefactos | [../99_ARCHIVE/WAVE3_ARTIFACT_RECOVERY.md](../99_ARCHIVE/WAVE3_ARTIFACT_RECOVERY.md) | CLOSURE / GAPS RETAINED |

## Auditoría reciente — YOUTUBE-AUTOMATION

- [Flow Script Processor: autopsia LIVE FAIL y plan de auditoría (2026-10-19)](../03_AUDITS/YOUTUBE_AUTOMATION_FLOW_SCRIPT_PROCESSOR_POST_LIVE_FAIL_2026-10-19.md) — LIVE FAIL del parsing, causa raíz reportada, fix local pendiente de revisión independiente; NO LIVE PASS.

## Agentes

Las fichas están en `01_AGENTS/`. El estado más reciente de los repositorios y su nivel de evidencia está en `03_AUDITS/FINAL_REPOSITORY_STATUS_MATRIX.md`.

## Proyectos

- `04_PROJECTS/YOUTUBE_AUTOMATION_CLOSURE.md`
- `04_PROJECTS/HANDS_V1_ARCHITECTURE_REFERENCE.md`
- `04_PROJECTS/EEC_MISSION_CONTROL.md`
- `04_PROJECTS/AUDIOBOOK_RIGHTS_RESEARCH.md`
- `04_PROJECTS/REVERSE_ENGINEERING_TEST_B_TRANSFER.md`
- `04_PROJECTS/FRUTINOVELAS_TO_HUMAN_AVATAR_AI.md`
- `04_PROJECTS/WAVE2_PROJECTS.md`
- `04_PROJECTS/WAVE3_PROJECT_RECOVERY.md`

## Prompts originales

Los cuatro originales principales están identificados por Library file_id en `05_PROMPTS/ORIGINAL_SOURCE_CATALOG.md`. Sus cuerpos no fueron reconstruidos ni truncados.

### Operational framework references — Wave 5

- `05_PROMPTS/CREATIVE_ENGINE_V1_OPERATIONAL_CONTRACT.md`
- `04_PROJECTS/REVERSE_ENGINEERING_TEST_B_TRANSFER.md`
- `04_PROJECTS/FRUTINOVELAS_TO_HUMAN_AVATAR_AI.md`

Estos documentos son referencias operativas/arquitectónicas y no sustituyen los cuerpos originales de los prompts.

## Wave 5 — corrección quirúrgica

Wave 5 preserva cuatro piezas que el cierre anterior identificaba como incompletas:

1. Reverse Engineering V1.2 — TEST B / Transfer.
2. Frutinovelas V2.0 → NOVELAS — HUMAN AVATAR AI.
3. HANDS V1.0 — arquitectura de referencia.
4. Creative Engine V1.0 — contrato operativo.

La onda no cambia la autoridad de los repositorios fuente, no aprueba automáticamente documentos `PROPOSED` y no convierte diseños en implementaciones verificadas.

## Sesiones

`06_SESSIONS/` conserva la evolución de la migración. Wave 4 documenta el cierre previo; Wave 5 añade referencias quirúrgicas sin reabrir la migración masiva.

## Reglas de lectura

1. Leer Bootstrap y CURRENT_STATE antes de actuar.
2. Usar el índice para localizar fuentes, no para convertir afirmaciones en hechos.
3. Verificar afirmaciones técnicas actuales en el repositorio fuente.
4. Mantener la distinción entre histórico, vigente, verificado, inferido y no verificado.
5. Solo `ALBRA8/CONTEXT` es escribible durante este flujo.
6. No introducir secretos.
7. Las referencias operativas no sustituyen los originales ni la fuente técnica de implementación.

## Historial

- 0.6.6 — auditoría documental actualizada de AGENTE-KREA V2.1 y gates pendientes.
- 0.6.0 — Wave 5: corrección quirúrgica y actualización del índice de conocimiento.
- 0.5.0 — Wave 4: cierre, matriz de repositorios, catálogo de fuentes originales y prueba de continuidad.
- 0.4.0 — Wave 3: evidencia directa adicional.
- 0.3.0 — Wave 2: ampliación estructural.

## AGENTE-GENERAL — cierre de seguridad

Contrato de controles y aceptación: `00_MASTER_CONTEXT/AGENTE_GENERAL_SECURITY_CLOSURE_PLAN.md`. Usar como checklist de implementación para autenticación, secretos/cifrado, dashboard, provider egress/SSRF, toolkit/planner, HITL, runtime y staging. El documento distingue requisitos pendientes de evidencia realmente verificada; no es certificación de producción.

## Pasada de repositorios — 2026-10-19

- [REPOSITORY_DELTA_REVIEW_2026-10-19.md](../03_AUDITS/REPOSITORY_DELTA_REVIEW_2026-10-19.md) — commits recientes de 14 repositorios accesibles, CI y ramas divergentes de AGENTE-GENERAL, inventario documentado de YOUTUBE-AUTOMATION, CRM-ALBRA y corrección de estado de Browser Factory. Es una revisión documental; no certifica todos los repositorios ni inspecciona el PC local.
- [FINAL_REPOSITORY_STATUS_MATRIX.md](../03_AUDITS/FINAL_REPOSITORY_STATUS_MATRIX.md) — la tabla Wave 4 se conserva como histórico; consultar su addendum 2026-10-19 para estado posterior.

## Evaluación de integración — SRZburner × NEX-SCOPE (2026-10-20)

- [SRZBURNER_NEX_SCOPE_INTEGRATION.md](../04_PROJECTS/SRZBURNER_NEX_SCOPE_INTEGRATION.md) — evaluación arquitectónica de la extensión SRZburner como fuente potencial de señales sociales para NEX-SCOPE. NEX-SCOPE es el candidato recomendado por su endpoint MCP, registro de herramientas, agente y contrato de procedencia. No hay evidencia confirmada de API/exportación autorizada de SRZ Club; no implementado ni aprobado en seguridad.

## Análisis comparativo — SRZburner × CAZADOR × NEX-SCOPE (2026-10-20)

- [SRZBURNER_CAZADOR_NEX_SCOPE_INTEGRATION_REVIEW.md](../04_PROJECTS/SRZBURNER_CAZADOR_NEX_SCOPE_INTEGRATION_REVIEW.md) — inspección directa del README y código relevante de CAZADOR y NEX-SCOPE. Conclusión: NEX-SCOPE es el propietario funcional; CAZADOR es opcional y no tiene soporte social específico demostrado. Se documenta que MCP requiere sesión autenticada, por lo que el acceso servicio-a-servicio también queda pendiente. No implementado.

## AGENTE-KREA — auditoría actual (2026-10-21)

- [AGENTE_KREA_CURRENT_AUDIT_2026-10-21.md](../03_AUDITS/AGENTE_KREA_CURRENT_AUDIT_2026-10-21.md) — KREA V2.1 como Product Factory comercial; historial de evolución, modelos de persistencia, findings sobre script E2E que solo devuelve exit 0, `db:push --accept-data-loss`, exposición potencial del Doctor, PDF/QA físico y proveedores externos aún no certificados. Solo documentación; sin cambios en KREA.

## NEX-SCOPE — estado arquitectónico actualizado (2026-10-21)

- [NEX_SCOPE_CURRENT_STATE_2026-10-21.md](../04_PROJECTS/NEX_SCOPE_CURRENT_STATE_2026-10-21.md) — actualización contra `ALBRA8/NEX-SCOPE` `main` @ `09f0cb18d259fc1ef6be007be61338b4350807ee`. Documenta la evolución a plataforma agentic/operable con MCP, memoria persistente, provenance/truth levels, execution trace, doctor, version visibility y E2E reportado. Mantiene separados los hechos verificados de las capacidades sociales aún no demostradas.


## AGENTE-KREA — estado actual revisado (2026-10-21)

- [AGENTE_KREA_CURRENT_STATE_2026-10-21.md](../04_PROJECTS/AGENTE_KREA_CURRENT_STATE_2026-10-21.md) — ficha de KREA V2.1 como Product Factory comercial. Registra el HEAD visible `460605e5b4966e5e9243c1ec3fcb4852c6758e1b`, los 241 tests/Golden Flows reportados (no reejecutados aquí), los proveedores externos NOT_VERIFIED, la discrepancia de `test:e2e` que solo hace echo y sale 0, y el riesgo operativo de `db:push --accept-data-loss`.

## Reauditoría cruzada — NEX-SCOPE + AGENTE-KREA (2026-10-21)

- [Registro de continuidad](../06_SESSIONS/2026-10-21_NEX_SCOPE_KREA_REAUDIT.md) — evolución, límites de evidencia y fronteras entre proyectos.
- [Auditoría canónica de AGENTE-KREA](../03_AUDITS/AGENTE_KREA_CURRENT_AUDIT_2026-10-21.md) — dictamen, secuencia de commits, findings y límites de verificación.
- [Ficha de estado de AGENTE-KREA](../04_PROJECTS/AGENTE_KREA_CURRENT_STATE_2026-10-21.md) — resumen de KREA V2.1 como Product Factory comercial; complementa la auditoría canónica.
- [Estado actual de NEX-SCOPE](../04_PROJECTS/NEX_SCOPE_CURRENT_STATE_2026-10-21.md) — agente, MCP, memoria persistente, provenance, execution trace y diagnóstico; no demuestra captura social nativa ni integración autorizada con SRZburner.

**Regla de vigencia:** los documentos de integración SRZburner del 2026-10-20 son antecedentes. Deben releerse junto con el estado actual de NEX-SCOPE; no representan autorización para implementar ni evidencia de integración existente.

## Instalador global + EEC — auditoría de integración (2026-10-22)

- [INSTALLER_EEC_AGENT_GENERAL_INTEGRATION_AUDIT_2026-10-22.md](../03_AUDITS/INSTALLER_EEC_AGENT_GENERAL_INTEGRATION_AUDIT_2026-10-22.md) — estado actual de INSTALADOR, EEC/PANEL-DE-CONTROL y AGENTE-GENERAL; distingue código existente de integración no implementada; documenta gates de runtime real, autenticación/RBAC, adapters y VPS.
- Decisión de arquitectura documentada: INSTALADOR = lifecycle; EEC = control plane; AGENTE-GENERAL = Seed/Host; agentes especializados = productos independientes; CONTEXT = documentación institucional separada, no miembro de EEC.


## CRM ALBRA — arquitectura Chat Marketing por capas (2026-10-24)

- [CRM_ALBRA_CHAT_MARKETING_LAYER_ARCHITECTURE_2026-10-24.md](../04_PROJECTS/CRM_ALBRA_CHAT_MARKETING_LAYER_ARCHITECTURE_2026-10-24.md) — decisión conceptual de conservar CRM ALBRA como Capa 1 y separar la ampliación en 2A Agéntico/Conversacional y 2B Superpowers/Chat Marketing. Incluye límites de responsabilidad, contrato/eventos por diseñar, fases de investigación y gate explícito de no construir aún.
- Estado: arquitectura conceptual acordada; auditoría actual de contratos, matriz definitiva e implementación pendientes.


## CRM ALBRA — cierre 2A y especificación 2B (2026-10-24)

- [CRM_ALBRA_2A_PILOT_CLOSURE_AND_2B_SPEC_2026-10-24.md](../04_PROJECTS/CRM_ALBRA_2A_PILOT_CLOSURE_AND_2B_SPEC_2026-10-24.md) — registra el piloto 2A como superado según el reporte del propietario, conserva `AGENTIC_2A_ENABLED=false`, documenta la observación `crmSynced: false` y autoriza únicamente la especificación de 2B, no su implementación.
- La ficha de 2B debe definir locks antes de encolar y justo antes del despacho, fallar cerrado ante estado no verificable, usar solo interfaces reales del CRM/2A, mantener la autoridad de despacho en el CRM y documentar consentimiento, supresiones e idempotencia.
