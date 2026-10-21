# NEX-SCOPE — estado actual verificado

- Fecha de auditoría: 2026-10-21
- Fuente canónica: `ALBRA8/NEX-SCOPE`
- Rama: `main`
- HEAD verificado: `09f0cb18d259fc1ef6be007be61338b4350807ee`
- Estado del repo: público, no archivado, rama `main`
- Estado de este documento: **DOCUMENTACIÓN DE SOLO LECTURA; NO IMPLEMENTA CAMBIOS EN NEX-SCOPE**

## 1. Motivo de esta actualización

La documentación de CONTEXT del 2026-10-20 describía NEX-SCOPE principalmente como plataforma de investigación de nichos de YouTube con agente, MCP y procedencia. Esa descripción quedó incompleta frente al estado actual verificable del repositorio.

La evolución importante no es solo cosmética: NEX-SCOPE incorporó una capa operable de agente, persistencia de memoria, trazabilidad de ejecución, diagnóstico operativo, visibilidad de versión y una batería E2E que cubre agente/MCP/procedencia/aislamiento.

Por tanto, las conclusiones arquitectónicas anteriores sobre SRZburner × CAZADOR × NEX-SCOPE deben conservarse como **histórico**, pero no tratarse como fotografía completa del NEX-SCOPE actual.

## 2. Evolución verificada

### 2.1 Base agentic + MCP

Commit `9aa103ee0a75223c27438eb5be4bc0c65c3d45ed` añadió una capa de agente conversacional operable y un servidor MCP HTTP/JSON-RPC.

Evidencia reportada en el commit:
- registro inicial de 14 herramientas;
- herramientas READ, WRITE, AI y YouTube;
- executor tipo ReAct con máximo 10 iteraciones y truncado de historial;
- streaming SSE en `/api/agent`;
- UI de chat agentic con estados de herramientas;
- `/api/mcp` compatible con MCP `2024-11-05`;
- 14 tools, 5 resources y 4 prompts;
- pruebas E2E de TypeScript/build, MCP y agente mono/multi-tool.

### 2.2 Hardening, procedencia y persistencia

Commit `c24aed2f95a62560150371828757b6f1fd89f312` añadió una segunda capa sustancial:

**Provenance / Truth Levels**
- `OBSERVED`
- `VERIFIED`
- `ESTIMATED`
- `MODELED`
- `INFERRED`
- `UNKNOWN`

Las respuestas de IA de keywords, trends, content-gaps, content-plan y monetization quedan etiquetadas mediante `_provenance`, y las herramientas equivalentes del agente aplican el mismo contrato. Los valores numéricos generados por el modelo se tratan explícitamente como estimaciones y no como datos oficiales de YouTube.

**MemoryDV**
- modelo Prisma `Memory` con persistencia real;
- metadatos de dominio, tipo, contenido, fuente, evidencia, procedencia, confianza, truth level, scope, estado, relevancia y `lastVerified`;
- operaciones `recordMemory`, `recallMemories`, `consolidateMemories` y `decayMemories`;
- `/api/memory` con GET/POST/DELETE y autenticación;
- aislamiento multiusuario;
- nuevas herramientas del agente `recall_memory` y `record_memory`.

**Execution Trace**
- modelo Prisma `ExecutionTrace`;
- registra agente, tarea, herramientas, entradas, salidas, errores, estado y tiempos;
- `startTrace`, `appendToolCall`, `finishTrace`;
- integración en `executor.ts`, de modo que las llamadas de herramientas y el estado final quedan reconstruibles.

**Doctor / observabilidad**
- `/api/doctor` ejecuta 10 comprobaciones operativas: database, auth, youtube_provider, ai_provider, cache, agent_runtime, memory, mcp, environment y build;
- `/api/version` expone nombre, versión, build date, git SHA, Node y entorno.

**Pruebas reportadas en ese commit**
- Auth: 9/9 PASS
- Multi-user isolation: 8/8 PASS
- Provenance: 5/5 PASS
- E2E research: 8/8 PASS
- Doctor: 9 OK + 1 WARN esperado por YouTube provider no configurado
- total reportado: 30 checks, 0 failures

Estas cifras son **evidencia reportada por el commit**, no un rerun independiente de esta auditoría.

### 2.3 Estado HEAD posterior

El HEAD actual `09f0cb18d259fc1ef6be007be61338b4350807ee` (2026-10-07) es posterior a `c24aed2...` y su mensaje indica actualización de rutas de agente, MCP, error/loading y archivos `lib/agent`, además de limpieza del directorio `upload/`.

El diff del commit visible en GitHub muestra principalmente limpieza de artefactos de ejecución/capturas y no evidencia una regresión de la arquitectura agentic descrita arriba.

## 3. Estado funcional actual que CONTEXT debe considerar

NEX-SCOPE ya no debe describirse únicamente como un "YouTube Niche Finder" pasivo.

La caracterización actual verificable es:

**Plataforma de inteligencia de nichos/YouTube con agente operativo, herramientas ejecutables, MCP, memoria persistente, procedencia explícita, trazabilidad de ejecución y diagnóstico operativo.**

Su núcleo sigue orientado a:
- investigación de nichos;
- keywords;
- tendencias;
- brechas de contenido;
- planes;
- monetización;
- análisis de competidores/canales;
- persistencia de conocimiento del usuario;
- exposición mediante agente y MCP.

## 4. Lo que NO se ha demostrado

La evolución anterior **no demuestra** todavía:

- ingestión social nativa de Instagram/TikTok/Facebook;
- integración autorizada con SRZburner o SRZ Club;
- API/SDK/exportación oficial de SRZburner;
- extractor de medios sociales equivalente a SRZburner;
- acceso servicio-a-servicio al MCP sin sesión de usuario;
- que CAZADOR sea necesario para alimentar NEX-SCOPE;
- que cualquier dato social pueda tratarse como `VERIFIED` sin evidencia de origen.

Por tanto, el cambio de arquitectura no debe inventarse a partir de la presencia de MCP/memoria/agente. Esas capacidades sí hacen a NEX-SCOPE un núcleo de inteligencia mucho más fuerte; no constituyen por sí solas un mecanismo de captura social.

## 5. Impacto sobre el análisis SRZburner × CAZADOR × NEX-SCOPE

La recomendación documentada el 2026-10-20 sigue siendo histórica y debe releerse con este estado nuevo.

La pregunta ya no es simplemente si NEX-SCOPE "puede analizar" datos sociales. Ahora también posee infraestructura propia para:

1. recibir conocimiento persistente;
2. ejecutar herramientas de agente;
3. conservar procedencia y niveles de verdad;
4. reconstruir ejecuciones;
5. exponer capacidades por MCP;
6. diagnosticar su runtime.

Esto fortalece significativamente su papel como **núcleo de inteligencia/orquestación de conocimiento**, pero todavía deja separada la cuestión de **captura/adquisición** de datos sociales.

No se aprueba con este documento la absorción de SRZburner ni la modificación de CAZADOR.

## 6. Frontera arquitectónica actual

La frontera recomendada queda así hasta nueva evidencia:

`fuente social autorizada / captura autorizada → ingesta normalizada → NEX-SCOPE (memoria + provenance + agent + analytics) → MCP → agentes ALBRA`

CAZADOR solo debe entrar como capturador si una auditoría posterior demuestra que su runtime, permisos y contrato pueden resolver una parte concreta de la adquisición social sin convertirlo artificialmente en un agente de inteligencia.

## 7. Evidencia y límites

Fuentes directas revisadas:
- README de `ALBRA8/NEX-SCOPE` en `main`;
- historial de commits de `main`;
- commit `9aa103ee...` — agentic chat + MCP;
- commit `c24aed2...` — provenance + memory + trace + doctor + version + tests;
- commit `09f0cb18...` — actualización posterior de rutas/agent/MCP y limpieza.

No se ejecutó NEX-SCOPE durante esta pasada. No se inspeccionó el PC local. Las cifras de tests son resultados reportados por el repositorio/commit y no certificación independiente.

## 8. Estado para decisiones futuras

- **NEX-SCOPE:** arquitectura agentic/operable claramente más madura que la reflejada en la documentación del 2026-10-20.
- **SRZburner:** fuente/capacidad externa todavía sin interfaz oficial de integración verificada.
- **CAZADOR:** extractor web general; soporte social específico sigue sin demostrarse.
- **YOUTUBE-AUTOMATION:** downstream audiovisual; no debe absorber esta capa de inteligencia social.
- **Implementación de integración SRZburner:** NO IMPLEMENTADA.
- **Aprobación de seguridad:** NO OTORGADA.
- **Siguiente auditoría útil:** actualizar la decisión de propietario funcional usando esta arquitectura actual y separar explícitamente captura, ingesta, memoria, análisis y exposición MCP.
