# AUDITORÍAS — HISTORIAL WAVE 2

## AGENTE-GENERAL — auditoría independiente pre-freeze

### Scope
Auditoría directa del repositorio `ALBRA8/AGENTE-GENERAL`, incluyendo arquitectura, seguridad, A2A, memoria, packaging, clean-room y documentación.

### Evolución de hallazgos

1. Se detectó inconsistencia de certificación respecto al SHA real.
2. Se detectó CI clean-room rojo por dependencia `httpx` ausente y fallos de tests.
3. Se determinó que `httpx` era la causa de los ocho fallos; Pydantic no era la causa.
4. Se cerró el problema de packaging/tests y CI pasó a verde.
5. Se detectó posteriormente un fail-open del perímetro de seguridad en `Agent._phase_check_permissions`.
6. Se detectó que el endpoint HTTP de execution podía llegar directamente a `agent.run` sin un perímetro explícito.
7. Constructor cerró ambos blockers en `8437f2a`, certificación documental `00a353c`.
8. Reporte final del constructor: 487 tests, CI green, parity 106/106, contamination clean, clean-room 17/17 + 2 optional.

### Estado de evidencia
- Los resultados finales son declaraciones del constructor hasta que una auditoría independiente posterior los reproduzca.
- CI reportado: run `38197913446` para el ciclo anterior y posteriormente run `38197913446` fue verificado como verde en el cierre previo; el ciclo final `8437f2a` fue reportado por el constructor con run `38197913446`.
- La migración conserva estos números como HISTORICAL REPORT, no como nueva certificación propia.

## KREA V2.1

Fuente: `KREA_FINAL_VERIFICATION.md`, commit `bb83dd2`.

Estado declarado por la fuente:
- 241/241 unit tests;
- integration/API/security/build PASS;
- clean-room PASS;
- LIPO PASS;
- 5 ghost layers non-blocking;
- LLM real NOT_VERIFIED;
- 2 visual QA checks NOT_VERIFIED;
- final status CONDITIONALLY APPROVED.

## AGENTE-LEADS

Fuente: README del repositorio.

Evidencia documental de pipeline determinístico de discovery → research → validation → evidence → dedup → scoring → intelligence → storage → report → CRM, con outreach opcional.

No se ha registrado aquí una auditoría independiente completa; por tanto el estado es VERIFIED DOCUMENTATION, no CERTIFIED.

## AGENTE-CHISMOSO

El repositorio contiene documentación extensa de arquitectura, clean-room, delivery, MCP y definición de terminado. Se requiere auditoría consolidada para determinar estado final y capacidades exactas.

## RADAR-SECOP2

README documenta evidencia, truth levels, MCP, MemoryDV, Doctor, skills, autonomía y pruebas unit/E2E/security/sync. No se ha migrado todavía un informe de auditoría independiente completo.

## DESPACHO-JURIDICO

README documenta el Agente Base V1.0, 30 órganos, MCP/MSP, memorydv, Doctor, evidence/provenance, HITL, workflows, handoffs, providers y seguridad. No se debe confundir la arquitectura base con conocimiento jurídico cargado ni con subagentes ya implementados.

## Estado de auditoría de Wave 2

- VERIFIED: evidencia directa de fuentes/repositorios.
- INFERRED: relaciones arquitectónicas derivadas de la conversación.
- NOT_VERIFIED: claims de tests/auditorías no reproducidos en este trabajo.
- ESTIMATED: nunca usar para declarar una capacidad real.
