# ALBRA-CONTEXT — Bootstrap

- Estado del documento: PROPOSED
- Versión: 0.3.0
- Propósito: punto de entrada universal para cualquier persona o IA autorizada que trabaje con el contexto externo de ALBRA.
- Repositorio maestro: `ALBRA8/CONTEXT`
- Escritura autorizada: exclusivamente este repositorio.

## 1. Propósito

ALBRA-CONTEXT es la memoria externa, versionada y portable de ALBRA. Conserva decisiones, estado operativo, referencias y aprendizajes reutilizables para que la continuidad no dependa de la memoria interna de una IA, una cuenta o una conversación.

El repositorio es almacenamiento y versionado; no es, por sí solo, la identidad completa de ALBRA. Los documentos canónicos contienen los detalles.

## 2. Regla de alcance

**La única ubicación que una IA que trabaje bajo este contexto puede crear, editar, borrar, subir o confirmar es `ALBRA8/CONTEXT`.**

Los demás repositorios son de **solo lectura** desde este trabajo. Se pueden consultar archivos, commits, documentación, issues, acciones y evidencias cuando sea pertinente, pero no modificarlos. Esto incluye expresamente `ALBRA8/AGENTE-GENERAL`.

## 3. Qué es ALBRA

ALBRA es un ecosistema de agentes y sistemas especializados que comparten una base arquitectónica común. El principio estructural es **especialización sin saturación**: una capacidad debe vivir donde corresponda y no añadirse a un agente solo porque sea técnicamente posible.

## 4. Agente General: la casa cognitiva universal

El Agente General es el organismo base universal de ALBRA. **No debe definirse principalmente como jefe, director u orquestador.**

La analogía oficial de trabajo es **una casa vacía pero completamente equipada**: tiene estructura, electricidad, agua, internet, seguridad, conectores, espacios y servicios fundamentales. Cuando llega un nuevo ocupante puede incorporar herramientas, conocimiento, software, MCP, APIs, procedimientos o capacidades sin reconstruir la casa.

Por tanto:
- **ADN:** lo que la casa es y las reglas que no puede perder.
- **Capacidades:** lo que puede incorporar o sustituir.
- **Especialización:** en qué misión se convierte al recibir conocimiento y capacidades de dominio.
- **Orquestación:** responsabilidad separable que puede existir en un sistema que la necesite.

## 5. ADN, capacidades y especialización

El ADN define identidad, invariantes, límites de seguridad, contratos fundamentales, trazabilidad y gobernanza.

Las capacidades pueden crecer sin límite práctico, pero no deben modificar silenciosamente el ADN. Pueden ser herramientas, MCP, APIs, proveedores, software, bases de datos, servicios, automatizaciones, agentes auxiliares o flujos.

Modelo conceptual:

`ALBRA CORE → CAPABILITY CONTRACT → CAPABILITY REGISTRY → ADAPTER / CONNECTOR → EXTERNAL CAPABILITY`

Los agentes especializados conservan el ADN común y añaden únicamente la misión, conocimiento, herramientas, políticas y workflows de su dominio.

## 6. Jerarquía de autoridad

1. ADN vigente y procedimiento aprobado para modificarlo.
2. Decisiones arquitectónicas aprobadas que no hayan sido sustituidas.
3. Especificaciones y contratos vigentes.
4. Estado operativo y documentación.
5. Propuestas y experimentos no aprobados.
6. Conversaciones y notas históricas, como procedencia y no como autoridad automática.

La evidencia factual se evalúa por separado. Si existe un conflicto que no pueda resolverse con autoridad y evidencia suficientes, se registra y se solicita una decisión.

## 7. Estados documentales

- `PROPOSED`: propuesta pendiente de aprobación.
- `APPROVED`: aprobada y vigente.
- `FROZEN`: protegida frente a cambios ordinarios.
- `SUPERSEDED`: sustituida.
- `DEPRECATED`: desaconsejada.
- `EXPERIMENTAL`: en evaluación.
- `HISTORICAL`: conservada por procedencia.
- `UNVERIFIED`: aún no verificada.

Aprobación normativa y verificación factual son conceptos distintos.

## 8. Protocolo de incorporación para una IA

### Antes
1. Leer este Bootstrap.
2. Comprobar rama, commit y estado de los documentos relevantes.
3. Leer [`00_MASTER_CONTEXT/CURRENT_STATE.md`](00_MASTER_CONTEXT/CURRENT_STATE.md).
4. Leer [`00_MASTER_CONTEXT/ALBRA_DNA.md`](00_MASTER_CONTEXT/ALBRA_DNA.md).
5. Consultar [`00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`](00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md).
6. Cargar solo agentes, proyectos, decisiones y auditorías pertinentes.
7. Verificar en el repositorio fuente cualquier afirmación técnica que deba considerarse actual.

### Durante
1. Separar hechos, declaraciones documentales, propuestas e hipótesis.
2. No atribuir aprobaciones al propietario que no hayan ocurrido.
3. No modificar repositorios externos a `ALBRA8/CONTEXT`.
4. No introducir secretos.
5. Registrar decisiones y conocimiento reutilizable cuando corresponda.

### Después
1. Verificar archivos y enlaces afectados.
2. Actualizar `CURRENT_STATE.md` si cambió el estado real.
3. Actualizar `KNOWLEDGE_INDEX.md` si nació una nueva fuente canónica.
4. Actualizar `CHANGELOG.md` cuando el cambio sea relevante.
5. Informar qué se cambió, qué se verificó y qué sigue pendiente.

## 9. Estructura actual

- `README.md` — navegación y propósito.
- `SECURITY.md` — seguridad y secretos.
- `CHANGELOG.md` — historial documental.
- `00_MASTER_CONTEXT/ALBRA_DNA.md` — ADN propuesto.
- `00_MASTER_CONTEXT/ALBRA_ARCHITECTURE.md` — arquitectura maestra.
- `00_MASTER_CONTEXT/CONTEXT_PROTOCOL.md` — protocolo de mantenimiento.
- `00_MASTER_CONTEXT/CURRENT_STATE.md` — estado actual.
- `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` — índice de fuentes.
- `00_MASTER_CONTEXT/GLOSSARY.md` — glosario.
- `01_AGENTS/AGENT_REGISTRY.md` — registro de agentes.
- `02_DECISIONS/DECISION_LOG.md` — decisiones.
- `03_AUDITS/README.md` — política de auditorías.
- `04_PROJECTS/README.md` — política de proyectos.
- `05_PROMPTS/README.md` — política de prompts.
- `06_SESSIONS/README.md` — política de sesiones.
- `99_ARCHIVE/README.md` — política histórica.

No crear carpetas o documentos por decoración; añadir contexto cuando exista contenido real que lo justifique.

## 10. Protección de AGENTE-GENERAL

El Seed de `AGENTE-GENERAL` queda fuera del alcance de modificación de este trabajo.

Referencias de solo lectura:
- Seed: `8437f2aa425a58fa76920ecc5c97ec99c85c0036`.
- Certificación documental: `00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e`.

No describir cifras de tests, auditorías o certificaciones como reproducidas independientemente salvo que se hayan ejecutado de nuevo y registrado.

## 11. Seguridad

`ALBRA8/CONTEXT` es público. Nunca almacenar contraseñas, tokens, claves API, claves privadas, cookies, credenciales, datos personales sensibles ni volcados de entorno. Usar marcadores ficticios en ejemplos.

## 12. Principio de continuidad

`ALBRA → ALBRA-CONTEXT → Bootstrap → contexto pertinente → repositorio fuente`

Una nueva IA puede reconstruir el contexto si tiene acceso al repositorio y a las fuentes necesarias. No se presupone memoria automática entre cuentas o modelos.

## 13. Próxima fase

1. Consolidar y aprobar el ADN.
2. Completar fichas de agentes con evidencia directa.
3. Completar fichas de proyectos relevantes.
4. Registrar decisiones arquitectónicas confirmadas por el propietario.
5. Documentar auditorías y verificaciones reproducibles.
6. Mantener `CURRENT_STATE` como fotografía viva del ecosistema.

## Historial

- 0.3.0 — Integró la arquitectura maestra, la casa cognitiva universal, el protocolo de contexto y el mapa documental ampliado.
- 0.2.0 — Explicitó el límite de escritura exclusiva en `ALBRA8/CONTEXT`.
- 0.1.0 — Primera versión de trabajo.
