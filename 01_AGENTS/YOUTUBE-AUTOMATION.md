# YOUTUBE AUTOMATION — FICHA DE CONTEXTO

- Repositorio: `ALBRA8/YOUTUBE-AUTOMATION`
- Estado de ficha: UPDATED WAVE 3 / VERIFIED FROM CURRENT README + HANDS DOC
- Rol: orquestador audiovisual / fábrica local de producción de vídeo.
- Estado histórico: EN PRUEBAS; arquitectura y código sujetos a cambios.

## Misión recuperada

Sistema de automatización audiovisual capaz de producir vídeos localmente mediante un pipeline que cubre guion, imágenes, locución, render, subtítulos, música y publicación opcional.

README V2.0 describe:

`guion → imágenes → locución → render Ken Burns → subtítulos → música → publicación opcional en YouTube`.

Incluye modo desde URL para analizar estructura de vídeos de referencia y generar contenido original, modo desde audio, avatares, editor de escenas, fábrica programada y kit multi-plataforma.

## Evolución funcional recuperada

- V2.0: fábrica local, pipeline audiovisual completo, scheduler/cola de ideas y autopublicación opcional.
- V2.1: Agente VÓRTICE conversacional para ejecutar acciones reales del motor.
- V2.1.1: sistema de Avatares PRO con 15 menús y consistencia de personaje/voz/estilo.
- Extensión Chrome Flow Script Processor documentada como puente de automatización Flow/ImageFX.
- HANDS evolucionó de capa aislada preparada para conexión a una integración parcial de contrato de ejecución.

## Arquitectura / capacidades observadas

- Backend FastAPI + dashboard local.
- Pipeline/orchestrator.
- Generación de guiones con Gemini.
- Generación de imágenes con Gemini y extensión Chrome como Plan B.
- TTS dual: Gemini TTS + edge-tts con fallback.
- Whisper para timestamps/subtítulos.
- FFmpeg para render.
- Scheduler/fábrica para producción programada.
- YouTube OAuth y programación.
- Modo URL: yt-dlp + Whisper + análisis de estructura con Gemini.
- Avatares consistentes y estilos visuales.
- Editor de escenas.
- Multi-plataforma mediante kit de descargas.
- Agente conversacional VÓRTICE.

## HANDS — estado más reciente verificable

`backend/services/hands/` existe y `docs/HANDS.md` declara:

**`PARTIAL CONNECTED: Execution Contract V1.0 contract layer (FlowOperator+ConfigGate+MockFlowControlAdapter TESTED; ExtensionBridgeDriver enabled=False por defecto; E2E real pendiente)`**.

Esto actualiza la descripción histórica anterior de “NOT CONNECTED”.

### Qué está verificado

- HANDS es capa de ejecución física controlada, no agente creativo.
- `HandsRuntime`, contratos, ActionEngine, DesktopOperator, FlowOperator, permisos, allowlists, workspace, waits, verification, recovery, locks, evidence, kill switch y sessions existen en la arquitectura documentada.
- Deny-by-default en application/domain/filesystem/command/browser.
- Shell prohibido (`allow_shell=False`).
- Workspace aislado y frontera de filesystem.
- Evidencia con hash/redacción y sesiones auditables.
- Desktop físico tiene triple candado: consent token + command map + allowlist.
- `MockDesktopBackend` y `MockFlowDriver` están TESTED.
- `ExtensionBridgeDriver` está TESTED contra servidor simulado con contrato HTTP real, pero permanece OFF por defecto.
- Execution Contract V1.0 está conectado a la capa mecánica y probado con mocks.
- `start_generation()` evalúa el gate de controles antes de `driver.submit` cuando existe `execution_spec` + adaptador.

### No verificado

- E2E real contra Flow/backend vivo.
- `PhysicalDesktopBackend` sobre un PC real.
- Capturas de pantalla reales.
- Integración física completa con YOUTUBE-AUTOMATION.
- Cross-host/live network A2A/Flow.

## Flow Script Processor — distinción importante

El README actual del repositorio documenta una extensión Chrome denominada **Flow Script Processor** dentro de `YOUTUBE-AUTOMATION/extension/`.

Su función documentada es automatizar Flow/ImageFX: vincular proyectos, detectar `out/ideas/idea_NNNNNN/script.json`, iniciar generación, observar resultados, aplicar cooldowns, reintentos y escribir assets en disco.

**No debe confundirse automáticamente con `ALBRA8/Extension-CAZADOR`.** La migración conserva ambas entidades separadas hasta que exista evidencia de que son el mismo proyecto.

## Límites / decisiones

- YOUTUBE-AUTOMATION sigue siendo el orquestador audiovisual.
- Desktop Operator y Flow Operator permanecen como operadores especializados, no superagentes.
- No crear un Video Editor Agent antes de probar HANDS en producción real.
- HANDS no inventa prompts/historias/escenas: recibe instrucciones operativas.
- Creative Engine decide WHAT crear; YOUTUBE-AUTOMATION decide HOW ejecutarlo.

## Frameworks relacionados

- YOUTUBE AUTOMATION CREATIVE ENGINE V1.0.
- Reverse Engineering Engine V1.2.
- Niche Blueprints.
- Production Specification.
- Production JSON.

## Estado de evidencia

**VERIFIED FROM CURRENT REPOSITORY DOCUMENTATION:** README V2.0/V2.1/V2.1.1 y `docs/HANDS.md`.

**PARTIAL / NOT_VERIFIED:** ejecución física real de HANDS y conexión E2E real con Flow.

**NOT_VERIFIED HERE:** auditoría integral nueva del HEAD del repositorio en esta Wave 3.
