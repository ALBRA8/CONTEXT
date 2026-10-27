# DESPACHO-JURIDICO — Auditoría de repositorio y estado del proyecto

- Fecha de revisión: 2026-10-26
- Repositorio fuente: [ALBRA8/DESPACHO-JURIDICO](https://github.com/ALBRA8/DESPACHO-JURIDICO)
- Rama revisada: `main`
- HEAD revisado: `277be56540dd7298f3ebc6a5654c2c7aaa1ba0c5`
- Tipo de revisión: inspección documental y estática de archivos/repositorio, más consulta de estado de GitHub Actions.
- Estado de la auditoría: **PARTIAL / NOT VERIFIED INDEPENDENTLY BY TEST EXECUTION**
- Alcance de escritura: solo documentación de `ALBRA8/CONTEXT`; no se modificó el repositorio del producto.

## 1. Dictamen ejecutivo

DESPACHO-JURIDICO no es un repositorio vacío: contiene una base de organismo ALBRA, módulos de runtime, reasoning, capacidades/herramientas, memoria, seguridad, evidencia/auditoría, comunicaciones, workflows, proveedores, harness/adapters y un piloto de corpus jurídico.

Sin embargo, la presencia de arquitectura y código no demuestra por sí sola que el sistema completo esté integrado, que todas las capacidades sean alcanzables desde el arranque, que las integraciones externas funcionen en producción ni que el corpus sea fiable para uso jurídico. Esta revisión encontró dos bloqueos prioritarios que impiden tratar el estado actual como una base legal certificada:

1. **CI:** la ejecución asociada al HEAD revisado aparece fallida y el workflow contiene una construcción que parece sintaxis inválida de YAML en el job `clean-room`. No se obtuvo un log de error que confirme la causa exacta; debe corregirse/verificarse el workflow y comprobar que GitHub crea y ejecuta los jobs.
2. **Procedencia del corpus:** el módulo `real_corpus_v11.py` declara hashes/procedencia del HTML oficial, pero el código inspeccionado construye `raw_content` a partir de fragmentos de texto declarados en Python y calcula el hash de ese contenido reconstruido. El flujo inspeccionado no acredita que el hash corresponda a los bytes originales del HTML oficial.

**Recomendación:** mantener el corpus piloto fuera de cualquier uso jurídico operativo hasta reconciliar la procedencia, auditar todos sus consumidores y reproducir los controles de admisión/rechazo. Resolver primero CI y trazabilidad del corpus.

## 2. Qué existe en el repositorio

La estructura consultada contiene aproximadamente 247 rutas en el árbol Git y, entre otros, los siguientes componentes:

- Núcleo `albra` con identidad/runtime, razonamiento, configuración, memoria, herramientas/capacidades, seguridad, auditoría/evidencia, resiliencia, workflows y comunicaciones.
- API y CLI, diagnósticos/Doctor y mecanismos de self-healing/self-improvement.
- Adaptadores de texto, voz, Telegram y WhatsApp; su presencia no acredita credenciales válidas ni E2E externo.
- Capa de adapters/bridges/harness, incluido un registro de conocimiento y contratos de tipos.
- Motor de conocimiento jurídico con módulos de ingestión, loader y retrieval providers.
- Piloto de corpus jurídico colombiano, tests y documentación de contexto.
- Scripts de inicio/despliegue y configuración de ejemplo.

El bootstrap registra `ZaiSdkBridge` como bridge prioritario y `PythonBridge` como fallback. La existencia de estas clases no prueba que el SDK externo, el fallback ni el ciclo completo de arranque se hayan verificado en un entorno limpio.

El proveedor de recuperación por defecto observado es en memoria; el loader principal declara su capa primaria vacía por diseño. Por ello, no se debe interpretar la existencia de ingestión/corpus piloto como prueba de que el conocimiento jurídico esté cargado y conectado de forma completa al recorrido operativo.

## 3. Hallazgo P0 — CI/workflow

Archivo inspeccionado: [`.github/workflows/ci.yml`](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/.github/workflows/ci.yml).

Dentro del job `clean-room` se observó una cadena multilínea tipo docstring directamente como propiedad YAML:

```yaml
clean-room:
  """Clean-room verification: clona el commit exacto
  que se certifica, no main ambiguamente.
  """
  runs-on: ubuntu-latest
```

Esta construcción parece no ser una propiedad YAML válida. Debe convertirse en un campo válido, por ejemplo `name:`, o en comentarios YAML. La ejecución de Actions consultada para el HEAD revisado figura como fallida: [run 40249056454](https://github.com/ALBRA8/DESPACHO-JURIDICO/actions/runs/40249056454). Otra ejecución histórica también figura fallida: [run 39740545128](https://github.com/ALBRA8/DESPACHO-JURIDICO/actions/runs/39740545128).

La consulta de jobs devolvió cero jobs para esas ejecuciones. Esto es compatible con un problema de parseo/validación del workflow, pero **no se recuperó un log que confirme la causa exacta**. Por tanto:

- Estado: **FAIL observado en Actions; causa raíz probable, no confirmada por log**.
- Los resultados históricos declarados `242/242 PASS`, `30 tests PASS` o `lint OK` no certifican el HEAD actual.
- No se ejecutó localmente la suite, lint ni clean-room durante esta revisión.

Acción requerida: validar el YAML, confirmar que GitHub reconoce los jobs y ejecutar la suite completa, lint y clean-room sobre un commit exacto. Registrar el hash probado y los enlaces de ejecución.

## 4. Hallazgo P0 — procedencia e integridad del corpus

Archivo inspeccionado: [`real_corpus_v11.py`](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/albra/knowledge/colombia/corpus_pilot/real_corpus_v11.py).

El encabezado atribuye contenido a HTML oficial y afirma que los hashes SHA-256 corresponden al HTML original. Sin embargo, el código inspeccionado:

- declara fragmentos de contenido jurídico dentro de estructuras Python;
- construye `raw_content` concatenando esos fragmentos;
- calcula un hash sobre el contenido construido, no sobre bytes originales del HTML descargado;
- incluye una declaración de hash adicional con apariencia de marcador/ejemplo que no se usa para reconstruir el contenido final;
- no conserva en el flujo inspeccionado los bytes originales del HTML ni una evidencia reproducible de su captura.

Esto no demuestra que cada fragmento sea falso; demuestra que **el método inspeccionado no autentica la procedencia original afirmada**. Hasta resolverlo, el corpus no debe presentarse como una ingestión verificada de HTML oficial.

También se identificaron módulos de corpus anteriores que requieren reconciliación con la fuente primaria:

- [`leyes_reales.py`](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/albra/knowledge/colombia/corpus_pilot/legislation/leyes_reales.py)
- [`jurisprudencia_real.py`](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/albra/knowledge/colombia/corpus_pilot/jurisprudence/jurisprudencia_real.py)

En el material de jurisprudencia se observó una atribución de C-251/2002 al derecho a la salud que contradice el propio historial de auditoría V1.1 del proyecto, donde esa entrada se señala como incorrecta y debe rechazarse. Esta contradicción requiere verificación directa contra la providencia oficial antes de emitir una conclusión jurídica definitiva.

El test `tests/test_v16_closure_integration.py` crea conocimiento jurídico sintético para pruebas. Eso puede ser válido como fixture, pero debe demostrarse que los fixtures sintéticos no pueden filtrarse a la recuperación de producción.

Acciones requeridas:

1. Poner en cuarentena los registros cuya procedencia no esté demostrada.
2. Recuperar cada fuente primaria oficial y conservar los bytes originales o un artefacto inmutable equivalente.
3. Definir qué bytes se hashean (respuesta HTTP exacta, archivo normalizado o texto extraído), registrar algoritmo, URL, fecha de captura, metadatos y transformaciones.
4. Validar identificador, artículo, texto, vigencia temporal, jurisdicción y fuente oficial de cada registro.
5. Separar claramente fixtures sintéticos, datos históricos y corpus admitido para uso.
6. Probar rechazo de hashes incorrectos, fuentes no autorizadas, artículos inventados, duplicados, textos alterados y normas derogadas.
7. Auditar todos los caminos de ingestión, carga, retrieval y consumo para impedir que registros no admitidos lleguen a respuestas jurídicas.

## 5. Arquitectura: qué está demostrado y qué no

### Evidencia de implementación estática

Se localizaron módulos para:

- runtime, identidad y razonamiento;
- capacidades y registro de herramientas;
- bridges/adapters y contratos de harness;
- ingestión, loader y proveedores de recuperación;
- seguridad, permisos, auditoría y evidencia;
- comunicaciones y API;
- diagnóstico y resiliencia.

### No demostrado por esta auditoría

- Que todas las rutas y módulos se conecten al arranque de forma funcional.
- Que el bridge ZAI y su fallback funcionen realmente bajo fallos y recuperación.
- Que permisos, aislamiento y límites de herramientas se cumplan en cada ruta operativa.
- Que las integraciones WhatsApp/Telegram o proveedores externos completen E2E real.
- Que el corpus piloto sea admisible para dar respuestas jurídicas fiables.
- Que el repositorio esté listo para producción o que todos los tests históricos sigan pasando.

## 6. Plan de cierre priorizado

### P0 — antes de ampliar conocimiento jurídico

- [ ] Corregir/validar `.github/workflows/ci.yml`.
- [ ] Ejecutar CI, lint, tests y clean-room sobre un commit exacto; conservar evidencia enlazada.
- [ ] Auditar el pipeline completo de admisión de corpus y bloquear el uso de registros no verificados.
- [ ] Reconciliar cada registro jurídico con fuente primaria oficial y vigencia temporal.
- [ ] Demostrar que fixtures sintéticos y corpus rechazado no son recuperables por la ruta operativa.

### P1 — antes de declarar integración operativa

- [ ] Probar arranque/parada del runtime, bridge prioritario, fallback y comportamiento ante proveedor caído.
- [ ] Verificar permisos, aislamiento, límites de herramientas, HITL y trazabilidad con pruebas negativas.
- [ ] Verificar los contratos entre harness, KnowledgeLoader, retrieval y respuestas de la API.
- [ ] Realizar E2E autorizado de canales externos en entorno controlado, sin confundir adaptadores presentes con conexiones reales.

### P2 — después de asegurar la base

- [ ] Ampliar corpus jurídico solo mediante ingestión trazable y verificable.
- [ ] Diseñar el pool de especialistas como propuesta separada, con contratos, límites y pruebas; no dar por implementados los subagentes solo porque se hayan definido nombres.
- [ ] Usar el proyecto como referencia de patrones de evidencia/provenance para un futuro agente contador, pero mantener separado el conocimiento contable y sus fuentes.

## 7. Relación con el futuro agente contador

La guía [`GUIA_FUTURO_AGENTE_CONTADOR.md`](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/CONTEXTO%20JURIDICO/GUIA_FUTURO_AGENTE_CONTADOR.md) continúa siendo una propuesta, no una especificación aprobada para implementación.

Patrones potencialmente reutilizables tras verificarlos: identidad/runtime, permisos, observabilidad, evidencia/provenance, ingestión, control temporal, abstención y resolución de conflictos.

No se debe copiar el conocimiento jurídico al agente contador. El dominio contable requiere fuentes primarias propias, reglas tributarias/contables independientes, pruebas de cálculo y validación de obligaciones y vigencia.

## 8. Límites de esta revisión

- Se inspeccionaron archivos y estructura GitHub y se consultó el estado de Actions.
- No se ejecutó el código localmente ni se reprodujeron tests, lint o clean-room.
- No se verificó cada norma/providencia contra su fuente primaria en esta pasada.
- No se realizó pentest ni E2E de canales externos.
- Los hallazgos son estáticos/documentales; deben conservarse como tales hasta que una nueva ejecución aporte evidencia.
- No se modificó ningún archivo en `ALBRA8/DESPACHO-JURIDICO`; este informe vive solo en `ALBRA8/CONTEXT`.

## 9. Fuentes principales

- [Repositorio fuente](https://github.com/ALBRA8/DESPACHO-JURIDICO)
- [Workflow CI](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/.github/workflows/ci.yml)
- [Corpus piloto V1.1](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/albra/knowledge/colombia/corpus_pilot/real_corpus_v11.py)
- [Módulo de legislación](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/albra/knowledge/colombia/corpus_pilot/legislation/leyes_reales.py)
- [Módulo de jurisprudencia](https://github.com/ALBRA8/DESPACHO-JURIDICO/blob/main/albra/knowledge/colombia/corpus_pilot/jurisprudence/jurisprudencia_real.py)
- [Contexto jurídico del repositorio fuente](https://github.com/ALBRA8/DESPACHO-JURIDICO/tree/main/CONTEXTO%20JURIDICO)
