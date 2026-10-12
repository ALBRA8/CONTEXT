# ALBRA-CONTEXT — Bootstrap

- Estado del documento: PROPOSED
- Versión: 0.2.0
- Propósito: punto de entrada universal para cualquier persona o IA autorizada que trabaje con el contexto externo de ALBRA.
- Repositorio maestro: https://github.com/ALBRA8/CONTEXT
- Alcance de escritura autorizado: exclusivamente este repositorio, `ALBRA8/CONTEXT`.

## 1. Propósito

ALBRA-CONTEXT es la memoria externa, versionada y portable de ALBRA. Conserva decisiones, estado operativo, referencias y aprendizajes reutilizables para que la continuidad no dependa de la memoria interna de una IA, una cuenta o una conversación.

El repositorio es almacenamiento y versionado; no es, por sí solo, la identidad completa de ALBRA. Este Bootstrap explica cómo orientarse y trabajar con el contexto. Los documentos canónicos contienen los detalles.

Este archivo es una versión de trabajo. No implica aprobación definitiva de todas las decisiones arquitectónicas que describe.

## 2. Regla de alcance: único repositorio modificable

**La única ubicación en la que una IA que trabaje bajo este contexto está autorizada a crear, editar, borrar, subir o confirmar cambios es `ALBRA8/CONTEXT`.**

Para cualquier otro repositorio:
- El acceso permitido es exclusivamente de lectura.
- Se permite consultar archivos, commits, documentación, issues, acciones y evidencias públicas cuando sea pertinente.
- Está prohibido crear, editar, borrar, subir archivos, abrir o modificar pull requests, cambiar configuraciones, gestionar ramas o realizar cualquier otra operación que cambie su estado.
- No se debe interpretar una petición general de avanzar en ALBRA como permiso para escribir fuera de `ALBRA8/CONTEXT`.

Esta restricción incluye expresamente `ALBRA8/AGENTE-GENERAL`. Sus referencias pueden consultarse para verificar hechos, pero sus archivos, ramas, configuraciones y estado no deben modificarse desde este trabajo. Una modificación futura requeriría una autorización explícita y una decisión nueva, documentada fuera de cualquier inferencia de la IA.

Si una tarea parece exigir una escritura fuera del repositorio maestro, detener esa parte y pedir instrucciones; no buscar atajos.

## 3. Reglas fundamentales

1. **Una fuente canónica por concepto.** Evitar copias divergentes; enlazar al documento que gobierna cada tema.
2. **Distinguir hechos, declaraciones y propuestas.** No presentar una afirmación documental como verificación independiente.
3. **Trazabilidad.** Las decisiones importantes deben registrar estado, justificación, alcance, fecha y referencias pertinentes.
4. **Cambios explícitos.** Una IA puede proponer cambios, pero no atribuirse aprobaciones humanas que no hayan ocurrido.
5. **Carga bajo demanda.** Leer primero este Bootstrap y después solo los documentos pertinentes a la tarea.
6. **Seguridad por defecto.** No publicar contraseñas, tokens, claves privadas, credenciales ni información sensible.
7. **Protección de artefactos.** No alterar artefactos declarados congelados ni asumir que el repositorio maestro concede permisos sobre otros sistemas.
8. **No inventar estado.** Si no se ha inspeccionado un archivo, commit, prueba o configuración, no afirmar que se comprobó.
9. **Cambios acotados.** Antes de escribir, comprobar el archivo actual y el alcance; después, verificar el resultado en GitHub.

## 4. Jerarquía, autoridad y conflictos

La autoridad normativa inicial es:
1. ADN vigente y procedimiento aprobado para modificarlo.
2. Decisiones arquitectónicas aprobadas que no hayan sido sustituidas ni contradigan el ADN.
3. Especificaciones y contratos vigentes.
4. Estado operativo y documentación.
5. Propuestas y experimentos no aprobados.
6. Conversaciones y notas históricas, que sirven como procedencia y no como autoridad automática.

La evidencia factual se evalúa por separado. Código, commits, pruebas y registros de ejecución pueden demostrar que una descripción operativa está desactualizada, pero no modifican por sí solos las reglas normativas.

Si existe una contradicción que no puede resolverse con autoridad y evidencia suficientes, registrar el conflicto y solicitar una decisión. No inventar una resolución.

## 5. Estados documentales

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

## 6. Protección de AGENTE-GENERAL

El Seed de AGENTE-GENERAL queda fuera del alcance de modificación de este repositorio. La creación o actualización de ALBRA-CONTEXT no autoriza editarlo, regenerarlo ni sustituirlo.

Referencias de solo lectura para seguimiento:
- Seed: commit [8437f2aa425a58fa76920ecc5c97ec99c85c0036](https://github.com/ALBRA8/AGENTE-GENERAL/commit/8437f2aa425a58fa76920ecc5c97ec99c85c0036).
- Certificación documental: commit [00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e](https://github.com/ALBRA8/AGENTE-GENERAL/commit/00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e).
- Documento de certificación: [docs/CERTIFICATION.md](https://github.com/ALBRA8/AGENTE-GENERAL/blob/00a353cee9602fe8929ed3d6f6b6055f0ae8cd9e/docs/CERTIFICATION.md).

Estas referencias identifican commits y documentación de certificación declarada. No afirman que todos los tests o auditorías hayan sido reproducidos independientemente en cada sesión.

## 7. Protocolo de incorporación para una IA

Antes de trabajar:
1. Leer este archivo y comprobar la rama, el commit y el estado de los documentos relevantes dentro de `ALBRA8/CONTEXT`.
2. Consultar [`00_MASTER_CONTEXT/CURRENT_STATE.md`](00_MASTER_CONTEXT/CURRENT_STATE.md) para conocer el estado registrado.
3. Consultar [`00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`](00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md) y leer solo las fuentes pertinentes.
4. Leer el ADN vigente cuando esté disponible y respetar su estado de aprobación.
5. Identificar las restricciones de seguridad y el alcance autorizado: escritura únicamente en `ALBRA8/CONTEXT`; lectura solamente en otros repositorios.
6. Separar hechos comprobados, declaraciones documentales, propuestas e hipótesis.

Durante el trabajo:
1. No asumir autoridad para aprobar cambios reservados al propietario.
2. No modificar repositorios externos a `ALBRA8/CONTEXT`.
3. No introducir secretos en archivos, commits, ejemplos ni registros.
4. Si falta información crítica, documentar la incertidumbre y preguntar.
5. Mantener los cambios pequeños, trazables y vinculados a su propósito.

Después del trabajo:
1. Verificar los archivos y enlaces afectados en `ALBRA8/CONTEXT`.
2. Actualizar el changelog cuando el cambio sea relevante.
3. Proponer actualizaciones documentales solo cuando exista conocimiento reutilizable.
4. Informar qué se cambió, qué se verificó y qué sigue pendiente.
5. No declarar pruebas, auditorías o aprobaciones que no se hayan realizado.

## 8. Estructura progresiva y archivos principales

No crear carpetas vacías solo para completar un esquema. Añadir documentos cuando exista contenido canónico que justifique su existencia.

Archivos base actuales:
- [`README.md`](README.md): presentación y navegación del repositorio.
- [`SECURITY.md`](SECURITY.md): manejo de secretos, límites de confianza y reporte de incidentes.
- [`CHANGELOG.md`](CHANGELOG.md): resumen de cambios relevantes.
- [`00_MASTER_CONTEXT/ALBRA_DNA.md`](00_MASTER_CONTEXT/ALBRA_DNA.md): línea base propuesta de identidad e invariantes.
- [`00_MASTER_CONTEXT/CURRENT_STATE.md`](00_MASTER_CONTEXT/CURRENT_STATE.md): estado actual registrado.
- [`00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md`](00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md): índice de fuentes.
- [`00_MASTER_CONTEXT/GLOSSARY.md`](00_MASTER_CONTEXT/GLOSSARY.md): definiciones de trabajo.

Los documentos del contexto maestro están en elaboración y no deben tratarse como aprobados hasta que su estado lo indique.

## 9. Próximos pasos

1. Revisar este Bootstrap y confirmar que la restricción de escritura exclusiva en `ALBRA8/CONTEXT` queda clara.
2. Contrastar la línea base propuesta de ADN con la información canónica que aporte el propietario.
3. Aprobar, corregir o mantener como propuesta cada principio del ADN.
4. Crear registros de decisiones cuando el propietario confirme decisiones que deban gobernar el sistema.
5. Documentar por separado la evidencia declarada y las verificaciones reproducidas.
6. Actualizar `CURRENT_STATE.md` cuando cambie el estado real del repositorio.

## 10. Historial

- 0.2.0 — Se explicitó el límite de escritura exclusiva en `ALBRA8/CONTEXT`, la lectura exclusiva de otros repositorios y el mapa de los archivos base existentes. Pendiente de revisión y aprobación.
- 0.1.0 — Primera versión de trabajo del Bootstrap.
