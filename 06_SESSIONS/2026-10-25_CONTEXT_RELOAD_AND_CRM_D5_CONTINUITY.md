# Sesión de continuidad — recarga de ALBRA-CONTEXT y CRM-ALBRA D.5

- **Fecha:** 2026-10-25
- **Propósito:** recargar el contexto canónico antes de continuar trabajo y registrar lo que debe mantenerse vigente para la siguiente interacción.
- **Fuente principal:** `ALBRA8/CONTEXT`, rama por defecto `main`.
- **Alcance:** lectura documental de CONTEXT y actualización de continuidad. No se ejecutó código de CRM-ALBRA ni se inspeccionó la máquina/DB operativa.
- **Estado de evidencia:** los contenidos documentales se leyeron directamente en GitHub; las pruebas, salidas locales y estados operativos citados por los informes siguen siendo evidencia reportada, no reejecutada por esta sesión.

## 1. Protocolo de incorporación aplicado

Se revisaron:
- `ALBRA_BOOTSTRAP.md`
- `00_MASTER_CONTEXT/CURRENT_STATE.md`
- `00_MASTER_CONTEXT/ALBRA_DNA.md`
- `00_MASTER_CONTEXT/ALBRA_ARCHITECTURE.md`
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`
- `00_MASTER_CONTEXT/MIGRATION_MISSING_INFORMATION.md)
- `03_AUDITS/FINAL_REPOSITORY_STATUS_MATRIX.md)
- Los documentos canónicos de CRM-ALBRA/Chat Marketing y la auditoría D.5.

Reglas incorporadas para continuidad:
1. Solo `ALBRA8/CONTEXT` es escribible en este flujo; repositorios de producto son fuentes de consulta.
2. Distinguir propuesta, decisión aprobada, reporte documental y verificación directa.
3. Un commit, un informe o un conteo de tests no equivale por sí solo a auditoría independiente ni a E2E de producción.
4. Mantener separación entre el ADN común de ALBRA y capacidades/agentes especializados. `ALBRA_DNA.md` y `ALBRA_ARCHITECTURE.md` siguen en `PROPOSED`, no son baseline final aprobada.
5. No añadir automáticamente una misión nueva a AGENTE-GENERAL ni tratarlo por defecto como jefe/orquestador universal.
6. No publicar secretos, credenciales, tokens ni datos personales sensibles en CONTEXT, que es público.

## 2. Modelo de ALBRA recuperado

ALBRA es un ecosistema de agentes y sistemas especializados que comparten principios y contratos comunes; no un único agente que lo hace todo. CONTEXT funciona como memoria externa versionada, registro de decisiones, estado, procedencia y límites de evidencia, pero no sustituye los repositorios de implementación.

El modelo arquitectónico documentado separa:
- ADN e invariantes comunes.
- AGENTE-GENERAL como Seed/Host transversal.
- Agentes especializados por dominio.
- Capacidades externas y sus adaptadores/contratos.
- Proyectos concretos y auditorías.

La referencia al Seed congelado histórico de AGENTE-GENERAL no debe confundirse con su HEAD posterior en evolución, cuyo estado documentado era `NOT_READY_TO_FREEZE` pendiente de CI/auditoría. El estado de los demás repositorios en la matriz también es un snapshot documental, no certificación actual automática.

## 3. CRM-ALBRA — arquitectura y estado de continuidad

La arquitectura institucional registrada distingue:
- **Capa 1:** CRM ALBRA/Chaleco, fuente canónica de datos comerciales, conversaciones, permisos, canales y autoridad de despacho.
- **Capa 2A:** motor agéntico/conversacional; debe decidir qué decir/hacer y realizar acciones comerciales solo por interfaces autorizadas.
- **Capa 2B:** motor de campañas/broadcast, audiencias, frecuencia, supresiones, métricas y estado operativo de campañas.

Las capas 2A y 2B no deben duplicar la fuente comercial ni abrir sockets de WhatsApp propios. Deben comunicarse por contratos explícitos; no inventar endpoints ni afirmar integraciones que no se hayan inspeccionado.

### Hitos documentados como reportes del propietario/Antigravity
- Piloto supervisado de 2A con regla determinística `keyword_rule`, latencia reportada de 321 ms y sin duplicados.
- MVP de 2B con 10/10 pruebas reportadas usando `MockDispatcher`, sin mensajes reales durante esa suite.
- Un piloto saliente real de 2B reportado y posterior mensaje entrante natural asociado al contacto/conversación.
- Estos hitos están documentados con límites de evidencia: no se reprodujeron desde esta sesión y no demuestran campañas masivas ni entrega/lectura por sí solos.

### Estado de seguridad que no se debe cambiar por inferencia
- `AGENTIC_2A_ENABLED=false` se reporta preservado.
- `allowRealDispatch=false` / `ALLOW_REAL_DISPATCH=false` se reporta preservado.
- La conversación piloto específica mantiene `isAutoReply=0` según el reporte.
- No enviar mensajes, activar respuestas automáticas, ampliar allowlists ni iniciar campañas sin autorización expresa separada.
- No restaurar el auto-reply de la conversación piloto sin autorización expresa.

## 4. Fase D.5 — adquisición atómica de despacho

Fuente canónica: [`CRM_ALBRA_D5_ATOMIC_DISPATCH_AUDIT_2026-10-25.md`](../04_PROJECTS/CRM_ALBRA_D5_ATOMIC_DISPATCH_AUDIT_2026-10-25.md).

Estado reportado: `VERIFIED WITH LIMITATIONS`. El informe describe una transacción SQLite que agrupa CAS a `dispatching`, inserción de nonce consumido y sello del nonce en metadata; además de rollback, concurrencia, anti-replay y fail-closed según pruebas reportadas.

Limitaciones críticas:
- La integración con el `PrismaClient` real sigue `NOT VERIFIED`; el test informado usa un mock traducido a SQL SQLite.
- No se ejecutó entrega E2E real a WhatsApp en D.5.
- La atomicidad local no vuelve atómica la escritura SQLite y el envío externo.
- No se documenta un reconciliador autónomo para propuestas huérfanas ni resultados ambiguos.
- Si Baileys pudo aceptar el mensaje pero falta confirmación local, la entrega es ambigua; no reintentar a ciegas.
- Los códigos HTTP dependen de la capa y punto de validación; deben preservarse mediante pruebas de contrato.

Los comandos y recuentos (incluido 14/14) se registran como **reportados por Antigravity**. Esta sesión no ejecutó esos comandos, no inspeccionó el árbol local ni consultó `db/custom.db`.

## 5. Próximos gates, sin autorización implícita

1. Mantener el despacho real desactivado.
2. Revisar contrato HTTP por escenario y compatibilidad de consumidores.
3. Definir recuperación controlada de `dispatching` huérfanos y de resultados ambiguos, sin reintento ciego.
4. Probar integración con PrismaClient real en entorno aislado.
5. Mantener separado el test unitario/mock de la integración de red y de la entrega externa.
6. No iniciar una siguiente fase, activar canales, modificar CRM-ALBRA o tocar DB operativa sin autorización expresa.
7. Verificar en el repositorio fuente cualquier afirmación técnica que se quiera elevar de reportada a actual/verificada.

## 6. Resultado de esta recarga

Se volvió a leer la fuente canónica y se consolidaron las fronteras de arquitectura, la jerarquía de autoridad, los niveles de evidencia y el estado de seguridad de CRM-ALBRA. Este documento es un registro de continuidad; no certifica código, pruebas locales, Prisma real ni estado de producción.
