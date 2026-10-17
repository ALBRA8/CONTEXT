# AGENTE-GENERAL — RECUPERACIÓN DIRIGIDA WAVE 3

- Repositorio: `ALBRA8/AGENTE-GENERAL`
- Clasificación: Seed universal / agente genérico domain-agnostic.
- **Estado de esta ficha Wave 3:** HISTÓRICO; no describe por sí sola el HEAD actual.
- **HEAD consultado 2026-10-17:** `7a71f0126331345d884ed140fec079c95b3840f4` (merge PR #57).
- **Certificación histórica:** `8437f2a425a58fa76920ecc5c97ec99c85c0036` / `00a353cee9602fe8929ed3d6f6b6055f0ae8cd9`; no atribuirla al HEAD actual.
- **Estado actual:** checks de GitHub del HEAD en éxito; documentación de certificación desactualizada; producción NOT VERIFIED.
- **Auditoría actual:** [`../03_AUDITS/AGENTE_GENERAL_FULL_AUDIT_2026-10-17.md`](../03_AUDITS/AGENTE_GENERAL_FULL_AUDIT_2026-10-17.md).

## Estado vigente según revisión del 2026-10-17

- El HEAD incorpora dashboard Next.js y provider settings API, almacén de configuración con Fernet y un daemon de WhatsApp basado en Baileys.
- `dashboard/src/server/openclaw-adapted.js` adapta utilidades de la extensión WhatsApp de OpenClaw bajo MIT (sesión persistente, escritura atómica/backup, backoff, watchdogs, lock y deduplicación). No es el runtime completo de OpenClaw.
- El repositorio actual incluye A2A HTTP y MCP; el servidor MCP concreto revisado es `InMemoryMCPServer`, mientras Handoff resuelve handlers in-process. No afirmar MCP remoto listo sin transporte real demostrado.
- `docs/CERTIFICATION.md` sigue informando 648 tests y fecha 2026-10-14 y contiene una afirmación ya obsoleta sobre dashboard fuera del repo. El merge actual reporta 744+ tests y la API de GitHub muestra checks exitosos, pero esta auditoría no ejecutó los tests localmente.
- `docs/ASR_LIMITATION.md` declara ASR no operativo por problemas de proveedores externos a fecha 2026-10-17; no se reprodujeron esos errores aquí.
- Recomendación: conservar el Seed; no sustituirlo íntegramente por Hermes/OpenClaw ni mantener dos cerebros centrales. Completar aceptación real de seguridad/HITL, actualizar certificación y probar integración con AGENTE-LEADS.

## Evolución recuperada (histórica; no representa el HEAD actual)

La historia documentada en CONTEXT y la evolución registrada durante la migración identifican esta secuencia de hitos:

`72dc5e4` → `4c642df` → `b272278` → `8af913e` → `1d84d8f` → `914c016` → `3b51385` → `089a1dc` → `072e22a` → `8437f2a` / `00a353c`.

Hitos funcionales recuperados:

- `72dc5e4`: Seed V1.0 con ADN universal transversal.
- `4c642df`: cierre de gaps de Feedback, Learning, Decision Memory, Skill Evolution, Birth/Readiness, Genetic Parity y Genetic Contamination.
- `b272278`: primera integración A2A.
- `8af913e`: cierre de interoperabilidad A2A HTTP/wire/routes/external fixture.
- `1d84d8f`: consolidación documental/certificación y limpieza de rutas/documentación; CI limpio aún pendiente de verificación externa.
- `914c016` / `3b51385`: cierre de gaps de seguridad A2A, integridad factual de Memory y forget/audit tombstones, con actualización documental.
- `089a1dc`: cierre de los 8 fallos clean-room relacionados con `httpx`, `tomllib`, FastAPI y contadores dinámicos.
- `072e22a`: certificación documental que apuntaba a `089a1dc`.
- `8437f2a`: cierre de los dos últimos bloqueadores independientes: fail-open del perímetro de seguridad y frontera HTTP de `/api/v1/execution`.
- `00a353c`: certificación final apuntando a `8437f2a`.

## ADN universal verificado/documentado

La certificación vigente declara:

- 106 componentes de Universal DNA.
- 8 namespaces de especialización permitidos.
- 3 módulos A2A auxiliares que no forman parte del DNA: `wire`, `exposition`, `observers`.
- Sin conocimiento de dominio especializado.

El Seed existe para ser especializado después del clonado, no para actuar como un superagente de dominio.

## Perímetro de seguridad final

El commit `8437f2a` documenta dos correcciones críticas:

1. Tool plan + ausencia simultánea de Firewall y Policy ⇒ `DENY`, nunca `ALLOW`.
2. `/api/v1/execution` se rehúsa a ejecutar cuando el Runtime no tiene perímetro de seguridad configurado y devuelve HTTP 503.

También se conservaron DEFAULT-DENY, HITL, Kill Switch, separación Autonomy/Permission, ausencia de bypass flags y redacción de tokens.

## Verificación final reportada por la fuente

`docs/CERTIFICATION.md` registra:

- 487 tests PASS.
- Python 3.10 / 3.11 / 3.12 PASS.
- Audit Matrix 40/40.
- Parity 106/106 y `is_pure_seed=True`.
- Contamination limpia.
- Clean-room 17/17 requeridos + 2/2 opcionales.
- CI GitHub Actions GREEN, 3/3 jobs, run `38197913446`.
- A2A: 81 tests.
- Security closure: 13 tests.

## Estado de evidencia

**VERIFIED FROM REPOSITORY:** commit `8437f2a`, certificación `00a353c`, números y gates presentes en `docs/CERTIFICATION.md`, y contenido del commit `8437f2a`.

**HISTORICAL / CONTEXT-RECOVERED:** secuencia completa de commits anteriores y decisiones de evolución registradas arriba.

**NOT_VERIFIED HERE:** no se ejecuta una nueva auditoría completa desde CONTEXT; esta ficha documenta evidencia recuperada, no crea una nueva certificación.

## Relación con ALBRA

- Es la base genética para crear agentes especializados.
- No debe absorber conocimiento de Legal, CRM, Trading, Krea, Leads o YouTube.
- La especialización debe añadirse fuera de `src/albra_seed/` y respetar el contrato de especialización.
- CONTEXT conserva continuidad documental; AGENTE-GENERAL conserva el runtime/seed técnico.
