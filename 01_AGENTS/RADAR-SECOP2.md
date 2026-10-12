# RADAR-SECOP2 — FICHA DE CONTEXTO

- Repository: `ALBRA8/RADAR-SECOP2`
- Status: IMPLEMENTED SYSTEM / VERIFIED DOCUMENTATION
- Role: detection and preparation of public-contracting opportunities in SECOP II Colombia.

## Capabilities
- Socrata ingestion from datos.gov.co SECOP II dataset.
- Adaptive field mapping, deduplication by process ID and SHA-256 modification detection.
- Deterministic filters by company profile.
- Explainable compatibility across seven dimensions.
- AI tender-document analysis using requirement/evidence/action states.
- Project drafting mode.
- Marco Lógico generation and validation.
- Telegram and WhatsApp channels, including multimodal inputs.
- MCP server for other projects to query opportunities/proposals/Marco Lógico.
- Evidence Contract and TruthLevel values VERIFIED/OBSERVED/INFERRED/ESTIMATED/UNKNOWN.
- Hard truth rule: `INFERRED + CUMPLE` is invalid; insufficient evidence degrades to PENDIENTE.
- MemoryDV with evidence requirement for factual memory.
- Skills contracts, Doctor, autonomy L0-L5, observability and human approval.

## Boundaries
The system does not automatically submit offers in SECOP; presentation remains manual. Dates are marked for verification when the public source does not publish them.

## Relationship
RADAR-SECOP2 is a domain system around public procurement intelligence. It overlaps with Leads/research/evidence patterns but has its own procurement domain and should not be collapsed into another specialist.

## Evidence status
The capabilities above are directly documented by the repository README. A complete independent audit and historical evolution record remain to be migrated.
