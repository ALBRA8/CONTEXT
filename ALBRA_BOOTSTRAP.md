# ALBRA-CONTEXT — Bootstrap

- Estado del documento: PROPOSED
- Versión: 0.1.0
- Propósito: punto de entrada para cualquier persona o IA autorizada que trabaje con el contexto externo de ALBRA.
- Repositorio: https://github.com/ALBRA8/CONTEXT

## 1. Propósito

ALBRA-CONTEXT es la memoria externa, versionada y portable de ALBRA. Conserva decisiones, estado operativo, referencias y aprendizajes reutilizables para que la continuidad no dependa de la memoria interna de una IA, una cuenta o una conversación.

El repositorio es almacenamiento y versionado; no es, por sí solo, la identidad de ALBRA. El Bootstrap explica cómo orientarse en el contexto. Los documentos canónicos contienen los detalles.

Este archivo es una primera versión de trabajo. No se debe interpretar como aprobación definitiva de todas las decisiones arquitectónicas que describe.

## 2. Reglas fundamentales

1. **Una fuente canónica por concepto.** Evitar copias divergentes; enlazar al documento que gobierna cada tema.
2. **Distinguir hechos, declaraciones y propuestas.** No presentar una afirmación documental como una verificación independiente.
3. **Trazabilidad.** Las decisiones importantes deben registrar estado, justificación, alcance, fecha y referencias pertinentes.
4. **Cambios explícitos.** Una IA puede proponer cambios, pero no atribuirse aprobaciones humanas que no hayan ocurrido.
5. **Carga bajo demanda.** Leer primero este Bootstrap y después solo los documentos pertinentes a la tarea.
6. **Seguridad por defecto.** No publicar contraseñas, tokens, claves privadas, credenciales ni información sensible.
7. **No alterar artefactos protegidos.** Trabajar en ALBRA-CONTEXT no autoriza cambios en el Seed congelado de AGENTE-GENERAL.

## 3. Jerarquía y conflictos

La autoridad normativa inicial es:
1. ADN vigente y procedimiento aprobado para modificarlo.
2. Decisiones arquitectónicas aprobadas que no hayan sido sustituidas ni contradigan el ADN.
3. Especificaciones y contratos vigentes.
4. Estado operativo y documentación.
5. Propuestas y experimentos no aprobados.
6. Conversaciones y notas históricas, que sirven como procedencia y no como autoridad automática.

La evidencia factual se evalúa por separado. Código, commits, pruebas y registros de ejecución pueden demostrar que una descripción operativa está desactualizada, pero no modifican por sí solos las reglas normativas.

Si existe una contradicción que no puede resolverse con autoridad y evidencia suficientes, registrar el conflicto y solicitar una decisión. No inventar una resolución.

## 4. Estados documentales

Usar cuando corresponda:
- `PROPOSED`: propuesta pendiente de aprobación.
- `APPROVED`: aprobada y vigente.
- `FROZEN`: protegida frente a cambios ordinarios.
- `SUPERSEDED`: sustituida por otra decisión o versión.
- `DEPRECATED`: desaconsejada, conservada por compatibilidad o referencia.
- `EXPERIMENTAL`: en evaluación.
- `HISTORICAL`: conservada por su valor histórico.
- `UNVERIFIED`: aún no verificada.

Mantener separadas la aprobación normativa y la verificación factual: una decisión puede estar aprobada aunque su implementación no se haya verificado.

## 5. Protección de AGENTE-GENERAL

El Seed de AGENTE-GENERAL queda fuera del alcance de modificación de este repositorio. La creación o actualización de ALBRA-CONTEXT no autoriza editarlo, regenerarlo ni sustituirlo.

Referencias declaradas para seguimiento:
- Seed: commit [8437f2aa425a58fa76920ecc5c97ec99c85c0036](https://github.com/ALBRA8/AGENTE-GENERAL/commit/8437f2aa425a58fa76920ecc5c97ec99c85c0036).
- Certificación documental: commit [00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e](https://github.com/ALBRA8/AGENTE-GENERAL/commit/00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e).
- Documento de certificación: [docs/CERTIFICATION.md](https://github.com/ALBRA8/AGENTE-GENERAL/blob/00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e/docs/CERTIFICATION.md).

Estas referencias identifican commits y documentación de certificación declarada. No afirman que esta sesión haya reproducido independientemente todos los tests o auditorías.

## 6. Protocolo de incorporación para una IA

Antes de trabajar:
1. Leer este archivo y comprobar la rama, el commit y el estado de los documentos relevantes.
2. Leer el ADN vigente cuando esté disponible.
3. Consultar las decisiones aplicables y el estado operativo.
4. Identificar las restricciones de seguridad y el alcance autorizado.
5. Separar los hechos comprobados de las declaraciones y las hipótesis.

Durante el trabajo:
1. No asumir autoridad para aprobar cambios reservados al propietario.
2. No modificar artefactos congelados.
3. No introducir secretos en archivos, commits, ejemplos ni registros.
4. Si falta información crítica, documentar la incertidumbre y preguntar.

Después del trabajo:
1. Verificar los cambios realizados y las referencias afectadas.
2. Proponer actualizaciones documentales solo cuando exista conocimiento reutilizable.
3. Registrar los cambios con mensajes de commit descriptivos.
4. Informar qué se cambió, qué se verificó y qué sigue pendiente.

## 7. Estructura progresiva prevista

No crear carpetas vacías solo para completar un esquema. Añadir documentos cuando exista contenido canónico que justifique su existencia.

Estructura objetivo orientativa:
- `ALBRA_BOOTSTRAP.md`: entrada universal y protocolo de incorporación.
- `README.md`: presentación del repositorio para personas.
- `SECURITY.md`: manejo de secretos, límites de confianza y reporte de incidentes.
- `CHANGELOG.md`: resumen de cambios relevantes.
- `00_MASTER_CONTEXT/ALBRA_DNA.md`: identidad, principios e invariantes, cuando su contenido canónico esté disponible.
- `00_MASTER_CONTEXT/CURRENT_STATE.md`: estado actual con referencias y fechas de comprobación.
- Directorios especializados para agentes, decisiones, auditorías, proyectos, prompts, sesiones y archivo, cuando sean necesarios.

## 8. Siguiente trabajo

1. Revisar este Bootstrap inicial.
2. Añadir `README.md`, `SECURITY.md` y `CHANGELOG.md`.
3. Incorporar el ADN canónico y el estado actual a partir de fuentes verificables, sin inventar contenido ausente.
4. Crear un registro de decisiones para las decisiones arquitectónicas aprobadas.
5. Documentar por separado la evidencia declarada y las verificaciones reproducidas.

## 9. Historial

- 0.1.0 — Primera versión de trabajo del Bootstrap. Pendiente de revisión y aprobación.
