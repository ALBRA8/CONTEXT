# AGENTE-GENERAL — Security Repair Queue

- Fecha: 2026-10-14
- Estado: OPEN / REPAIR QUEUE
- Contexto: reauditoría del Seed universal antes de freeze.

## Hallazgos pendientes

### P0 — Memory trust boundary
1. Las memorias recuperadas estaban entrando al contexto del proveedor con autoridad `SYSTEM`.
2. Deben tratarse como datos no confiables (`TOOL`/equivalente) para impedir que contenido persistido actúe como instrucciones de máxima autoridad.

### P0 — Last-mile output security
3. Falta una compuerta final obligatoria de redacción de secretos antes de devolver la respuesta al usuario.
4. La compuerta debe ser fail-closed cuando el pipeline de sanitización falle.

### P0 — Provider adapters
5. Revisar/corregir serialización del rol `TOOL` para OpenAI, Anthropic, Groq, Ollama, ZAI y Mock.
6. Verificar que ningún adapter degrade el límite de confianza de memoria recuperada.

### P1 — Sanitization consistency
7. `redact_secrets()` y `assess_suspicion()` presentan criterios potencialmente desalineados: algo puede ser redactado sin quedar clasificado como sospechoso.
8. Unificar o documentar explícitamente la relación entre ambos mecanismos.

### P1 — Retrieval scale
9. El pool de candidatos de recuperación está acotado alrededor de 10.000 elementos.
10. Evaluar estrategia de escalado/recuperación para evitar excluir memoria antigua pero relevante a gran escala.

### P1 — Certification drift
11. `docs/CERTIFICATION.md` debe actualizarse después de cerrar las reparaciones y ejecutar la auditoría final.
12. No reutilizar el estado histórico `8437f2a / 487 tests / READY_TO_FREEZE` como certificación del estado actual.

## Orden operativo recomendado

1. Memory -> TOOL / trusted-boundary correction.
2. Provider adapters.
3. Last-mile secret redaction + fail-closed.
4. Sanitizer/suspicion alignment.
5. Retrieval hardening/documentation.
6. Auditoría completa del estado final.
7. Test de seguridad real.
8. Actualización de certificación.
9. Freeze del Seed.
10. Solo después: clonado/especialización de agentes.

## Nota arquitectónica

El objetivo no es seguir agregando capacidades al General indefinidamente. El objetivo es cerrar correctamente la infraestructura universal una sola vez para que CRM, Leads, Krea, Legal y demás especializados hereden el Seed estable y añadan únicamente su especialización de dominio.

## HITL

El bloque de endurecimiento operativo HITL recientemente incorporado no se considera un fallo estructural del Seed. Mantiene requisitos explícitos para cifrado real, backups, KMS/separación de claves, autenticación/RBAC, controles de red, restore drills y reconciliación de efectos externos inciertos; esos puntos siguen siendo requisitos de entorno/producción, no evidencia de que el entorno real ya esté configurado.

## Estado de freeze

NO FREEZE todavía. El Seed debe permanecer abierto hasta cerrar P0/P1, completar la auditoría final y reconciliar la certificación con el commit real.
