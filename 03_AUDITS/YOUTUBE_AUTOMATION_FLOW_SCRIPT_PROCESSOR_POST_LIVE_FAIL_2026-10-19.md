# YOUTUBE-AUTOMATION — Flow Script Processor: autopsia LIVE FAIL y plan de auditoría

- **Fecha documental:** 2026-10-19
- **Proyecto fuente:** `ALBRA8/YOUTUBE-AUTOMATION`
- **Repositorio escribible para este registro:** `ALBRA8/CONTEXT` únicamente
- **Estado del registro:** HISTORICAL + CURRENT INVESTIGATION
- **Implementación local reportada:** IMPLEMENTATION READY FOR REVIEW
- **Auditoría independiente del diff:** PENDING — no se recibió todavía el diff íntegro en esta operación
- **LIVE PASS del fix:** NO EXISTE / NO EJECUTADO
- **Commit del fix en YOUTUBE-AUTOMATION:** NO REPORTADO; los cambios se describen como locales, sin commit
- **Constructor:** NO INVOLUCRADO

## 1. Resumen ejecutivo

La extensión Chrome **Flow Script Processor 2.6.0**, trabajando en la rama `harness-v1`, completó una generación real de video en Google Flow, pero la telemetría de la extensión no extrajo metadatos de la solicitud `eb1hJf`. La generación real sí ocurrió; la extracción de metadatos falló. Por ello, el resultado fue **LIVE FAIL para el objetivo de observabilidad/parsing**, no un fallo de generación de video.

La autopsia comunicada por Antigravity identificó como causa raíz probable y concreta un supuesto incorrecto en el interceptor de `window.fetch`: Flow habría llamado `fetch(new Request(...))` sin un segundo argumento `init`, mientras el interceptor solo examinaba `init.body`. La corrección local reportada agrega inspección pasiva de `Request` mediante `clone().text()` y manejo de varios tipos de cuerpo. Esa corrección todavía requiere revisión del diff y pruebas antes de autorizar commit o una nueva prueba LIVE.

## 2. Estado de origen reportado

- Repositorio: `ALBRA8/YOUTUBE-AUTOMATION`.
- Rama: `harness-v1`.
- HEAD base reportado: `85ab666bc283509c1617feabefc98aee3bac5ed4`.
- Parent reportado: `8326f43ebd54f3a6fb41e4b74041422c44955eb8`.
- Extensión: Flow Script Processor `2.6.0`.
- ID de extensión reportado: `eejhfkoibnkfnkiomejgcggclofceobp`.
- Ubicación canónica local reportada: `D:\\PROYECTOS\\YOUTUBE AUTOMATION\\extension`.
- Backend local reportado: `http://127.0.0.1:8000`, versión `2.20.0`, health true; Gemini true; Nvidia true (`moonshotai/kimi-k3)); Whisper true.

**Nivel de evidencia:** estos valores proceden de informes de la sesión y no se volvieron a consultar directamente en el PC ni se inspeccionó el working tree mediante esta operación. No presentarlos como comprobación independiente actual.

## 3. LIVE FAIL: evidencia de la sesión

Durante una ejecución real en Google Flow con la extensión 2.6.0:

- Se utilizó un Start Frame real.
- El video se generó y completó en Flow.
- La red mostró solicitudes `WuwhI`, `eb1hJf`, `jwpduf` y `as29s`.
- La telemetría reportada fue:

```json
[
  {"kind":"rpc_request","rpcid":"WuwhI","ts":1792464488597,"videoModelKey":null,"startFrame":null},
  {"kind":"rpc_request","rpcid":"eb1hJf","ts":1792464493895,"videoModelKey":null,"startFrame":null},
  {"kind":"rpc_response","rpcid":"eb1hJf","ts":1792464497576},
  {"kind":"rpc_response","rpcid":"as29s","ts":1792464534960,"ids":{},"states":{}}
]
```

Los campos relevantes reportados como nulos o vacíos fueron `operation`, `videoModelKey`, `aspectRatio`, `count`, `promptPresent`, `mediaIdPresent`, `fingerprintPresent` y crop; el resultado de `as29s` no contenía IDs ni estados extraídos.

**Veredicto histórico:** `LIVE FAIL` para la ingesta/telemetría de metadatos. No significa que Flow no generara el video.

**Seguridad observada en ese reporte:** no se informó fuga de credenciales ni de Media IDs crudos. Esto es una observación del informe, no una auditoría independiente del log completo.

## 4. Autopsia técnica reportada

La solicitud real `eb1hJf` se habría enviado con un cuerpo codificado como formulario URL-encoded, en el parámetro `f.req`, conteniendo un batch envelope y un array posicional anidado:

```text
f.req
└── batch envelope
    └── [0] = ["eb1hJf", "<payload_protobuf_string>", null, "generic"]
        └── JSON payload
            └── [0][0][0] = positional spec (longitud 6)
                [0] prompt envelope
                [1] modelo ("abra_i2v_10s" o "abra_i2v_6s")
                [2] aspect ratio
                [3] count
                [4] start-frame envelope
                    [1] Media ID sin procesar en payload de origen
                    [5] crop
                [5] linked ingredients/assets
```

La causa raíz identificada por el implementador fue el siguiente patrón conceptual:

```js
const rawBody = init ? init.body : null;
```

Si Flow llama `fetch(request)`, `init` es `undefined`, por lo que el interceptor nunca inspecciona el cuerpo de la solicitud. Al llegar un cuerpo nulo a `rpcBodyText`, `parseFreqEntries` produce una lista vacía y los parsers F1/F2/posicional no llegan a ejecutarse con la carga útil real.

Esta explicación es coherente con el comportamiento estándar de Fetch, pero el código fuente/diff del interceptor y la solicitud completa no se inspeccionaron directamente durante esta documentación. La conclusión queda registrada como **FORENSIC ROOT CAUSE IDENTIFIED — implementer-reported, pending diff review**.

## 5. Parser posicional previo: comportamiento documentado históricamente

La implementación previa reportada ya tenía soporte para payloads F1/F2 y arrays posicionales asociados a `eb1hJf`:

- `resolvePositionalSpec(root)` extrae modelo en `spec[1]`, relación de aspecto en `spec[2]`, cantidad en `spec[3]), Start Frame en `spec[4]) y assets/ingredientes enlazados en `spec[5]`.
- El envelope de Start Frame usa `[4][1]` para el Media ID y `[4][5]` para crop.
- `sanitizeCrop` acepta objeto o array `[top,left,bottom,right]`.
- La detección de operación evita falsos positivos por coincidencia con `MEDIA_GENERATION_PAYGATE_TIER`.
- El fingerprint usa FNV-1a de 12 hexadecimales.
- La regla de seguridad es no emitir Media IDs crudos: se deben hashear inmediatamente.

Tests previamente reportados para la implementación anterior: F1 64/64, F2 67/67, suite nueva inicial 38/38, harness 55/55, total 224/224. Estos resultados son históricos y no demuestran que la ingesta real `fetch(Request)` funcionara; el LIVE posterior mostró lo contrario.

## 6. Fix local post-autopsia reportado

Antigravity informó cambios locales sin commit en:

1. `extension/injector.js`
2. `tests/flow_batchexecute_eb1hjf_mock.js`
3. `tests/test_flow_startframe_eb1hjf.py`

La descripción de implementación incluye:

- helpers `isUrlSearchParams`, `isFormData`, `isBlob`, `isRequest`;
- extensión de `rpcBodyText` para detectar streams, tipos de cuerpo cross-realm, URLSearchParams/FormData y objetos cuyos nombres de constructor coincidan con `QueryData|HttpParams|Params`;
- selección del cuerpo explícito `init.body` cuando existe;
- si no existe un cuerpo explícito y `input` es un `Request`, clonarlo y llamar `text()` sobre el clon;
- lectura de Blob mediante `text()`;
- preservar la llamada real mediante `originalFetch.apply(this, arguments)`;
- soporte en `parseFreqEntries` para formulario URL-encoded, JSON crudo, `%5B` y espacios/`+`.

Antigravity reportó estas pruebas locales después del fix:

- F1: 64/64.
- F2: 67/67.
- suite dual/post-autopsia: 49/49.
- harness: 55/55.
- total reportado: 235/235.

La suite nueva supuestamente incluye observación de `fetch(Request)`, extracción de modelo, fingerprint del Start Frame, normalización de crop, comprobación de que el Request original mantiene `bodyUsed === false`, URLSearchParams, QueryData duck-typed, no consumo de ReadableStream y ausencia de Media UUID crudo.

**Clasificación:** resultados y descripción de implementación reportados por Antigravity. No se volvieron a ejecutar los comandos ni se inspeccionaron los archivos en el PC durante esta operación. El total 235/235 no es todavía una certificación independiente.

## 7. Auditoría técnica independiente: riesgos y criterios de aceptación

Antes de aprobar commit o LIVE, revisar el diff íntegro y comprobar como mínimo:

1. **Semántica Fetch efectiva.** Distinguir `fetch(url, init)` de `fetch(Request)`. Cuando existe `init.body` explícito, inspeccionar ese cuerpo efectivo; no leer el cuerpo del Request que puede quedar reemplazado por `init`.
2. **No interferencia.** El interceptor debe llamar a `originalFetch` con los argumentos y el contexto originales, sin sustituir URL, método, headers, credentials, señal, redirect ni body; la telemetría no debe cambiar el resultado de la solicitud real.
3. **Request.clone().text().** Confirmar que solo se lee el clon, que el Request original no queda consumido, y que el manejo de errores asíncronos no produce rechazos sin capturar, bloqueos, duplicados o carreras de orden.
4. **Streams y backpressure.** No consumir destructivamente streams ni leer un body arbitrario sin límite. Verificar qué ocurre con cuerpos grandes, streams no clonables, errores de lectura y límites de memoria. No asumir que detectar `getReader` equivale a manejarlo de forma segura.
5. **Tipos de cuerpo.** Revisar por separado URLSearchParams, FormData, Blob, Request, ReadableStream y wrappers de terceros. No confiar únicamente en `constructor.name`, `toString()` arbitrario ni duck typing sin validación de forma y límites. No registrar contenidos completos.
6. **Parseo de f.req.** Probar batch envelope, codificación percent-encoded, signos `+`, espacios, JSON crudo, entradas malformadas, payload ausente y múltiples entradas; evitar falsos positivos.
7. **Regresión del parser.** Preservar F1, F2 y el parser posicional; verificar operación, modelo, aspect ratio, count, Start Frame, crop e ingredientes.
8. **Privacidad.** El Media ID crudo nunca debe llegar a telemetry, console, errores, logs, snapshots de tests ni informes. Emitir únicamente fingerprint/hash y flags de presencia. No registrar cookies, tokens, headers sensibles, URLs privadas ni cuerpos completos.
9. **Aislamiento de fallos.** Una falla de parseo/telemetría no debe fallar, reintentar ni alterar la generación real de Flow.
10. **Pruebas reales vs mocks.** Identificar qué suites ejecutan el injector real y cuáles prueban únicamente funciones mockeadas. El éxito de mocks no sustituye una prueba LIVE controlada.

Estas son condiciones de revisión, no hallazgos confirmados del diff todavía no recibido.

## 8. Restricciones de arquitectura y seguridad vigentes

- No modificar backend, AGENTE-GENERAL, bridge, contratos, queues, orchestrator, HANDS, auth/cookies/transport.
- No cambiar `main`, ni fusionar ramas.
- No fabricar solicitudes `batchexecute` ni reproducir auth/transport de Flow.
- No emitir IDs crudos de media.
- No involucrar al constructor en esta fase.
- No autorizar LIVE ni commit antes de revisar el diff y el reporte técnico.
- Solo se permite escribir en `ALBRA8/CONTEXT` durante este flujo documental.

## 9. Próximo paso autorizado

Realizar **auditoría técnica del diff post-autopsia** en Antigravity, en modo estrictamente de solo lectura:

- congelar el working tree;
- confirmar branch, HEAD y estado Git;
- mostrar el diff completo real de los tres archivos modificados, sin elipsis;
- revisar semántica Fetch, Request/streams, parseo, privacidad y regresiones;
- ejecutar y reportar comandos exactos, distinguiendo tests reales del injector de mocks;
- emitir uno de estos veredictos: `AUDIT APPROVED`, `AUDIT APPROVED WITH CONDITIONS` o `AUDIT REJECTED`;
- detenerse al terminar.

Hasta nuevo permiso explícito: **NO COMMIT, NO LIVE, NO MERGE, NO CONSTRUCTOR**.

## 10. Estado final de este registro

Este documento preserva conjuntamente el reporte de ejecución, la autopsia reportada, el estado del fix local y los criterios independientes de revisión. No certifica el diff ni declara corregido el fallo LIVE. La siguiente IA debe pedir/leer el informe de auditoría técnica y comparar el diff real antes de autorizar cualquier avance.
