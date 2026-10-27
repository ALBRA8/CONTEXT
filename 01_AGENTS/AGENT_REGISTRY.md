# Registro de agentes ALBRA

- Estado: UPDATED WAVE 3
- Versión: 0.3.0
- Regla: este registro documenta repositorios y funciones observadas; no concede permisos ni sustituye la documentación de cada agente.

| Agente / sistema | Repositorio | Rol documentado / observado | Estado |
|---|---|---|---|
| Agente General | `ALBRA8/AGENTE-GENERAL` | Seed universal técnico y operativo | FROZEN reference — Wave 3 evidence |
| Agente Leads | `ALBRA8/AGENTE-LEADS` | Inteligencia y prospección B2B | PARTIALLY VERIFIED — current README + historical audit |
| Agente Krea | `ALBRA8/AGENTE-KREA` | Product Factory / Commercial Product Engine | VERIFIED DOCUMENTATION — V2.1 |
| Agente Chismoso | `ALBRA8/AGENTE-CHISMOSO` | Investigación / inteligencia | PARTIAL — Wave 3 architecture + DoD evidence |
| YouTube Automation | `ALBRA8/YOUTUBE-AUTOMATION` | Orquestador/fábrica audiovisual | PARTIAL — current README + HANDS |
| Trading | `ALBRA8/AGENTE-TRADING` | Trading institucional SMC + MT5 bridge | VERIFIED DOCUMENTATION |
| Despacho Jurídico | `ALBRA8/DESPACHO-JURIDICO` | Organismo jurídico base | IMPLEMENTED BASE / legal knowledge and specialist subagents not loaded |
| Agente Contador | Repositorio por identificar | Contabilidad y control financiero empresarial | PROPOSED / IN DEVELOPMENT DECLARED; implementation NOT VERIFIED |
| Community Manager | Repositorio por identificar | Marca, comunidad, contenidos y reputación | PROPOSED / USER-DECLARED INITIATIVE; implementation NOT VERIFIED |
| CRM ALBRA | `ALBRA8/CRM-ALBRA` | CRM comercial multi-tenant + IA | VERIFIED DOCUMENTATION / current README |
| NEX-SCOPE | `ALBRA8/NEX-SCOPE` | YouTube niche discovery SaaS | SYSTEM / SAAS — not classified as agent |
| RADAR-SECOP2 | `ALBRA8/RADAR-SECOP2` | Inteligencia de oportunidades SECOP II | VERIFIED DOCUMENTATION |
| Extension-CAZADOR | `ALBRA8/Extension-CAZADOR` | Browser/capture/scraping utility | VERIFIED DOCUMENTATION / tool-system |
| PURO-PODER-CONOCIMIENTO | `ALBRA8/PURO-PODER-CONOCIMIENTO` | Knowledge corpus/system | VERIFIED HIGH-LEVEL / not proven autonomous agent |

## Relationships relevant to Wave 3

- Krea creates/products digital products; legal/rights clearance is not automatically part of Krea.
- Leads and Chismoso both perform research/intelligence, but their responsibilities remain distinct and should not be overloaded without a domain decision.
- YOUTUBE-AUTOMATION is the audiovisual orchestrator; HANDS is its physical execution layer.
- Flow Script Processor is documented inside YOUTUBE-AUTOMATION and must not be conflated with the standalone Extension-CAZADOR repository.
- NEX-SCOPE is a discovery/analysis SaaS platform feeding YouTube decisions, not the audiovisual orchestrator.
- PURO-PODER-CONOCIMIENTO is a knowledge corpus, separate from CONTEXT.
- Despacho Jurídico is a base organism with architecture for future legal knowledge and specialist subagents; those specialists are not yet proven implemented by the README.

## Evidence rule

1. Do not infer full capabilities from names.
2. Direct repository documentation can establish `VERIFIED DOCUMENTATION`; it does not automatically establish fresh execution verification.
3. Historical audits remain historical evidence.
4. Preserve `VERIFIED`, `PARTIAL`, `INFERRED`, `ESTIMATED`, `NOT_VERIFIED` and `MISSING` distinctions.

## Historial

- 0.3.0 — Wave 3 deepened Krea, Leads, Chismoso, YouTube/HANDS, Extension-CAZADOR, NEX-SCOPE and PURO-PODER-CONOCIMIENTO evidence.
- 0.2.0 — Wave 2 added fichas and relationships adicionales sin eliminar Wave 1.
- 0.1.0 — Registro inicial.


## Iniciativas de soporte empresarial — 2026-10-27

- Ficha de planificación: [ALBRA_BUSINESS_SUPPORT_AGENTS_PLAN_2026-10-27.md](../04_PROJECTS/ALBRA_BUSINESS_SUPPORT_AGENTS_PLAN_2026-10-27.md).
- Contador y Community Manager se registran como iniciativas declaradas por el propietario; no se identificaron sus repositorios fuente ni se verificó su implementación.
- Despacho Jurídico tiene una base técnica existente, pero la auditoría del 2026-10-26 conserva bloqueos P0 y no certifica corpus ni ejecución independiente.
