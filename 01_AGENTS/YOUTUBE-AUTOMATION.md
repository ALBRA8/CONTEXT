# YOUTUBE AUTOMATION — FICHA DE CONTEXTO

- Repositorio: `ALBRA8/YOUTUBE-AUTOMATION`
- Estado de ficha: PARTIAL / VERIFIED FROM REPOSITORY README + CONVERSATION
- Rol: orquestador audiovisual / fábrica local de producción de vídeo.
- Estado histórico: EN PRUEBAS; arquitectura y código sujetos a cambios.

## Misión recuperada

Sistema de automatización audiovisual capaz de producir vídeos localmente mediante un pipeline que cubre guion, imágenes, locución, render, subtítulos, música y publicación opcional.

README V2.0 describe:

`guion → imágenes → locución → render Ken Burns → subtítulos → música → publicación opcional en YouTube`.

También incluye modo desde URL para analizar la estructura de vídeos de referencia y generar contenido original, y modo desde audio.

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
- Agente conversacional VÓRTICE en la evolución documentada del proyecto.

## HANDS

El repositorio incorpora `backend/services/hands/`.

HANDS V1.0 fue diseñado como capa física aislada, con:

- deny-by-default;
- permisos y allowlists;
- sesiones;
- locks;
- evidencia;
- kill switch;
- contratos de ejecución;
- observación/verificación.

Estado histórico registrado en la conversación: BUILT · TESTED · AUDITED · NOT CONNECTED. Posteriormente el usuario confirmó que HANDS ya estaba subido al repositorio tras correcciones.

La decisión explícita fue: primero dotar de manos a YOUTUBE-AUTOMATION y probar una producción real; no crear todavía un VIDEO EDITOR AGENT separado.

## Límites / decisiones

- YOUTUBE-AUTOMATION sigue siendo el orquestador audiovisual.
- Desktop Operator y Flow Operator permanecen como operadores especializados, no superagentes.
- No crear un Video Editor Agent antes de probar HANDS en producción real.
- No conectar HANDS automáticamente durante la fase de preparación.
- El sistema no debe confundirse con Creative Engine: Creative Engine decide WHAT crear; YOUTUBE-AUTOMATION decide HOW ejecutarlo.

## Frameworks relacionados

- YOUTUBE AUTOMATION CREATIVE ENGINE V1.0.
- Reverse Engineering Engine V1.2.
- Niche Blueprints.
- Production Specification.
- Production JSON.

## Evidencia pendiente

- Auditoría integral más reciente del repositorio y estado exacto post-HANDS.
- Estado exacto de conexión de HANDS después de las últimas correcciones.
- Ficha completa de providers, MCP, A2A, permisos y tests actuales.
