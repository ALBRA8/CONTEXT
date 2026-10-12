# ALBRA Context Protocol

- Estado: PROPOSED
- Versión: 0.1.0
- Propósito: definir cómo una persona o IA incorpora, consulta, actualiza y verifica el contexto externo de ALBRA.

## 1. Fuente de continuidad

`ALBRA8/CONTEXT` es la fuente externa versionada de continuidad documental de ALBRA. No sustituye al código de los agentes ni a sus repositorios; registra el conocimiento transversal necesario para reconstruir el contexto de trabajo.

## 2. Secuencia de incorporación

1. Leer `../ALBRA_BOOTSTRAP.md`.
2. Comprobar rama, commit y estado documental.
3. Leer `CURRENT_STATE.md`.
4. Leer `ALBRA_DNA.md` y cualquier documento normativo relevante.
5. Consultar `KNOWLEDGE_INDEX.md`.
6. Cargar únicamente los agentes, proyectos, decisiones y auditorías relacionados con la tarea.
7. Verificar en el repositorio fuente cualquier afirmación técnica que deba considerarse actual.

## 3. Tipos de conocimiento

- **Normativo:** ADN, principios y decisiones aprobadas.
- **Factual:** hechos respaldados por evidencia.
- **Operativo:** estado actual de repositorios y proyectos.
- **Conocimiento:** capacidades, arquitectura y documentación reutilizable.
- **Histórico:** sesiones, propuestas sustituidas y experimentos.

## 4. Regla de procedencia

El contexto resume fuentes externas. Cuando un dato técnico pueda cambiar, conservar la referencia al repositorio, archivo, commit o documento fuente. Una síntesis en este repositorio no convierte por sí sola una afirmación en evidencia independiente.

## 5. Cambios

Antes de modificar contexto:
- identificar el concepto canónico afectado;
- comprobar su versión actual;
- evitar duplicar fuentes;
- distinguir propuesta de aprobación;
- registrar decisiones importantes en `02_DECISIONS/`;
- actualizar `CHANGELOG.md` cuando el cambio sea relevante.

## 6. Conflictos

Si una conversación histórica contradice una decisión aprobada, prevalece la decisión vigente. Si dos fuentes de autoridad comparable contradicen una a otra, registrar el conflicto y solicitar una decisión. No resolverlo por inferencia.

## 7. Portabilidad entre cuentas

Una nueva cuenta de IA puede reconstruir el contexto leyendo este repositorio, siempre que tenga acceso al repositorio y a las herramientas necesarias. No se presupone memoria compartida entre cuentas.

## 8. Seguridad

Nunca incorporar secretos, tokens, contraseñas, cookies, claves privadas ni credenciales. Las referencias a capacidades externas deben describir interfaces y estados, no valores secretos.

## Historial

- 0.1.0 — Protocolo inicial de incorporación y mantenimiento.
