# ALBRA-CONTEXT — Bootstrap

- Estado del documento: CLOSURE / READY FOR OPERATIONAL USE
- Versión: 0.4.0
- Propósito: punto de entrada universal para cualquier persona o IA autorizada que trabaje con el contexto externo de ALBRA.
- Repositorio maestro: `ALBRA8/CONTEXT`
- Escritura autorizada: exclusivamente este repositorio.

## 1. Propósito

ALBRA-CONTEXT es la memoria externa, versionada y portable de ALBRA. Conserva decisiones, estado operativo, referencias, evidencia y aprendizajes reutilizables para que la continuidad no dependa de la memoria interna de una IA, una cuenta o una conversación.

## 2. Regla de alcance

**La única ubicación que una IA que trabaje bajo este contexto puede crear, editar, borrar, subir o confirmar es `ALBRA8/CONTEXT`.**

Los demás repositorios son fuentes de consulta. Se pueden inspeccionar archivos, commits, documentación, issues, acciones y evidencias cuando sea pertinente, pero no modificarlos durante este flujo.

## 3. Qué es ALBRA

ALBRA es un ecosistema de agentes y sistemas especializados que comparten una base arquitectónica común. El principio estructural es **especialización sin saturación**: una capacidad debe vivir donde corresponda y no añadirse a un agente solo porque sea técnicamente posible.

## 4. Agente General

El Agente General es el Seed universal de ALBRA, comparable a una casa vacía pero equipada: conserva la estructura, contratos, seguridad, trazabilidad y capacidades transversales; la especialización se incorpora después.

No debe definirse principalmente como jefe, director u orquestador. Orquestación es una responsabilidad separable.

### Estado histórico importante

El Seed congelado histórico documentado fue:

- código: `8437f2aa425a58fa76920ecc5c97ec99c85c0036`
- certificación: `00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e`

Pero **ese ya no es el HEAD actual**.

El repositorio `ALBRA8/AGENTE-GENERAL` tiene un HEAD posterior conocido:

`43563c81c7a77bb9a762921037fd7f91414cb212`

Ese commit integra memoria en el reasoning loop y declara en su propio mensaje `NOT_READY_TO_FREEZE` pendiente de CI/auditoría. Por tanto, una IA debe distinguir siempre entre **Seed congelado histórico** y **HEAD posterior en evolución**.

## 5. ADN, capacidades y especialización

El ADN define identidad, invariantes, límites de seguridad, contratos fundamentales, trazabilidad y gobernanza.

Las capacidades pueden crecer sin límite práctico, pero no deben modificar silenciosamente el ADN. Pueden ser herramientas, MCP, APIs, proveedores, software, bases de datos, servicios, automatizaciones, agentes auxiliares o flujos.

## 6. Jerarquía de autoridad

1. ADN vigente y procedimiento aprobado para modificarlo.
2. Decisiones arquitectónicas aprobadas que no hayan sido sustituidas.
3. Especificaciones y contratos vigentes.
4. Estado operativo y documentación actual.
5. Propuestas y experimentos no aprobados.
6. Conversaciones y notas históricas como procedencia, no como autoridad automática.

La evidencia factual se evalúa por separado.

## 7. Estados documentales

- `PROPOSED`: propuesta pendiente de aprobación.
- `APPROVED`: aprobada y vigente.
- `FROZEN`: protegida frente a cambios ordinarios.
- `SUPERSEDED`: sustituida.
- `DEPRECATED`: desaconsejada.
- `EXPERIMENTAL`: en evaluación.
- `HISTORICAL`: conservada por procedencia.
- `UNVERIFIED`: aún no verificada.
- `PARTIAL`: evidencia útil pero incompleta.
- `MISSING`: fuente o dato no recuperado.
- `MISSING_BINARY`: binario exacto solicitado no recuperado.

Aprobación normativa y verificación factual son conceptos distintos.

## 8. Protocolo de incorporación para una IA

### Antes
1. Leer este Bootstrap.
2. Comprobar rama y commit de CONTEXT.
3. Leer `00_MASTER_CONTEXT/CURRENT_STATE.md`.
4. Leer `00_MASTER_CONTEXT/ALBRA_DNA.md`.
5. Consultar `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`.
6. Consultar `00_MASTER_CONTEXT/MIGRATION_MISSING_INFORMATION.md` si la tarea depende de historia o gaps.
7. Cargar solo agentes, proyectos, decisiones y auditorías pertinentes.
8. Verificar en el repositorio fuente cualquier afirmación técnica que deba considerarse actual.

### Durante
1. Separar hechos, declaraciones documentales, propuestas e hipótesis.
2. No atribuir aprobaciones al propietario que no hayan ocurrido.
3. No modificar repositorios externos a `ALBRA8/CONTEXT` durante este flujo.
4. No introducir secretos.
5. Mantener historia y estado actual separados.

### Después
1. Verificar archivos y enlaces afectados.
2. Actualizar `CURRENT_STATE.md` si cambió el estado real.
3. Actualizar `KNOWLEDGE_INDEX.md` si nació una nueva fuente canónica.
4. Actualizar `CHANGELOG.md` cuando el cambio sea relevante.
5. Informar qué se cambió, qué se verificó y qué sigue pendiente.

## 9. Estructura documental de cierre

- `00_MASTER_CONTEXT/` — ADN, arquitectura, protocolo, estado, índice y gaps.
- `01_AGENTS/` — fichas y registro de agentes.
- `02_DECISIONS/` — decisiones.
- `03_AUDITS/` — auditorías, trazas y matriz final.
- `04_PROJECTS/` — proyectos y estados.
- `05_PROMPTS/` — frameworks y catálogo de originales.
- `06_SESSIONS/` — sesiones históricas relevantes.
- `99_ARCHIVE/` — artefactos históricos y recuperación de fuentes.

## 10. Prompts originales

Los cuatro originales principales existen en Library y están identificados por file_id en `05_PROMPTS/ORIGINAL_SOURCE_CATALOG.md`.

No se deben reconstruir ni presentar como originales si el cuerpo no está disponible.

## 11. Seguridad

`ALBRA8/CONTEXT` es público. Nunca almacenar contraseñas, tokens, claves API, claves privadas, cookies, credenciales, datos personales sensibles ni volcados de entorno.

## 12. Regla de evidencia

Usar siempre el nivel de evidencia apropiado:

`VERIFIED` → `VERIFIED DOCUMENTATION` → `PARTIAL` → `INFERRED` → `ESTIMATED` → `NOT_VERIFIED` → `MISSING` / `MISSING_BINARY`.

No elevar un estado por repetición de una afirmación histórica.

## 13. Prueba de cuenta nueva

Una IA nueva debe poder responder desde CONTEXT qué es ALBRA, qué es el ADN, qué agentes/proyectos existen, cuál es su estado, qué decisiones están vigentes, qué auditorías existen, qué prompts están disponibles, qué está verificado y qué sigue pendiente.

Cuando la respuesta dependa de una fuente externa o de un binario no recuperado, debe decirlo explícitamente.

## 14. Estado de cierre

**CONTEXT READY FOR OPERATIONAL USE**

La declaración significa **completo en función de la información recuperable y trazable**. No significa 100% de toda la historia externa ni disponibilidad de todos los binarios históricos.
