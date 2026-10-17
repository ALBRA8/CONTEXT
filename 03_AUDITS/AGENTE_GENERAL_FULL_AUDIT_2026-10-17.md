# Auditoría documental y revisión estática — AGENTE-GENERAL

- **Fecha:** 2026-10-17
- **Estado:** PARTIAL / CODE REVIEW COMPLETED; PRODUCTION NOT VERIFIED
- **Repositorio fuente:** https://github.com/ALBRA8/AGENTE-GENERAL
- **HEAD consultado:** 7a71f0126331345d884ed140fec079c95b3840f4
- **Commit:** merge PR #57 — provider settings, adaptación selectiva de utilidades OpenClaw y ASR fixes.
- **Límite de escritura:** únicamente ALBRA8/CONTEXT. AGENTE-GENERAL fue fuente de solo lectura.

## 1. Alcance y método

Se revisó el árbol recursivo del repositorio actual (510 entradas), README, arquitectura, seguridad, matriz de auditoría, LIPO, pyproject.toml, certificación, contratos A2A, servidor MCP, Handoff, rutas de provider settings, almacén cifrado, dashboard, daemon de WhatsApp, adaptación de OpenClaw, avisos de terceros, límites de ASR y workflows de CI. Se consultó el estado de checks de GitHub asociado al HEAD indicado.

Esto es una revisión documental y estática del código disponible en GitHub. No se clonó ni ejecutó localmente el repositorio, no se probaron credenciales reales, no se verificó el deployment ni se hicieron envíos reales por WhatsApp. Los resultados de CI se identifican como evidencia de GitHub, no como pruebas reproducidas por esta revisión.

## 2. Dictamen ejecutivo

**No se recomienda reemplazar íntegramente AGENTE-GENERAL por Hermes Agent u OpenClaw con la evidencia actual.** El HEAD actual de AGENTE-GENERAL ya incorporó un dashboard y una adaptación selectiva de utilidades de OpenClaw para resolver problemas concretos del canal WhatsApp. Por tanto, la decisión ya no es simplemente “construir todo desde cero” frente a “instalar OpenClaw”: ALBRA ha reutilizado partes de OpenClaw conservando su propio runtime, contratos y perímetro de seguridad.

El repositorio sigue teniendo valor como Seed transversal de ALBRA: identidad, contratos, ejecución, memoria, evidencia, procedencia, seguridad, aprobaciones y auditoría. Hermes merece una comparación funcional posterior si la necesidad es reemplazar el motor de agente, pero todavía no hay una prueba equivalente que demuestre que una migración completa reduzca coste o mantenimiento sin perder garantías.

El objetivo del propietario es un único agente base que organice el ecosistema ALBRA, no mantener dos agentes centrales en paralelo. La arquitectura debe distinguir el Seed reusable de la responsabilidad de orquestación: el Bootstrap de CONTEXT indica que AGENTE-GENERAL no debe definirse principalmente como jefe/director/orquestador y que la orquestación es una responsabilidad separable. La decisión sobre un único punto de entrada de ALBRA debe explicitarse sin convertir todo el Seed en un agente monolítico.

## 3. Estado y evidencia de CI

### Evidencia observada

- HEAD actual: 7a71f0126331345d884ed140fec079c95b3840f4, fechado por GitHub el 2026-10-17.
- El mensaje del merge informa “744+ passed” y 5/5 jobs de CI en verde; esto es una declaración del commit, no un conteo que esta auditoría haya reproducido.
- La API de GitHub mostró 8 checks completados con conclusión success: Python 3.10, 3.11, 3.12, compatibilidad Windows, build de dashboard/escaneo de secretos del bundle público, workflow dashboard-build y dos checks de Dependabot.
- Evidencia consultable: https://github.com/ALBRA8/AGENTE-GENERAL/commit/7a71f0126331345d884ed140fec079c95b3840f4/checks

### Inconsistencia documental

docs/CERTIFICATION.md todavía señala como última verificación el 2026-10-14, commit probado de PR #36 y 648 tests; además afirma que el dashboard no está incluido en el repositorio. En el HEAD actual, el dashboard sí está versionado bajo dashboard/, con workflow propio y rutas de provider settings. La certificación y algunos documentos de seguridad están desactualizados respecto del HEAD. **No se debe declarar producción certificada hasta actualizar la certificación canónica y verificar aceptación real del entorno.**

## 4. Capacidades encontradas

El README y el código muestran una semilla genérica, no un agente especializado de dominio, con estas capas:

- **Núcleo/runtime:** identidad, configuración, ciclo de vida y loop de agente.
- **Cognición/proveedores:** reasoning, routing, fallback, proveedores mock y adaptadores externos; NVIDIA NIM se añadió como proveedor.
- **Memoria y evidencia:** memoria, procedencia, evidencia, clasificación factual y reglas para no elevar automáticamente información desconocida a hecho.
- **Seguridad:** firewall default-deny, políticas, RBAC/permisos, gestión de secretos, redacción, Kill Switch y aprobaciones HITL.
- **Ejecución/operación:** runner, trazas, idempotencia/replay, workflows, jobs, scheduler, resiliencia, doctor y recuperación.
- **Integración:** MCP, Handoff, A2A, API REST, CLI, archivos y capacidades multimodales.
- **Dashboard:** aplicación Next.js versionada, API de chat, ajustes de proveedor y proxy para daemon WhatsApp.

El README declara 106 entradas de Universal DNA y una matriz con 40 probes. La matriz del repositorio indica 40/40 VERIFIED; esta revisión no volvió a ejecutar esos probes.

## 5. Hallazgos de integración: MCP, Handoff y A2A

### MCP — abstracción implementada; transporte real aún por demostrar

src/albra_seed/mcp/server.py declara una interfaz abstracta MCPServer y una implementación concreta InMemoryMCPServer. El comentario del código deja explícito que una capa de especialización puede sustituir el transporte por JSON-RPC/HTTP sin modificar cliente o registro. Por tanto, no debe describirse el HEAD como un servidor MCP remoto listo para conectar solo por tener el protocolo, registry y servidor en memoria.

### Handoff — delegación local

HandoffManager resuelve handlers desde AgentRegistry dentro del proceso. Tiene contratos, timeout, evidencia y auditoría, pero no es por sí mismo un transporte remoto.

### A2A — camino de interoperabilidad más avanzado

El Seed incluye cliente/servidor A2A, contratos, AgentCard, transporte HTTP e in-process, traducción hacia Handoff y filtro de seguridad. El servidor A2A documenta idempotencia de tareas, deadline, autorización, firewall, policy, HITL, autonomía, auditoría, procedencia y evidencia. El handler por defecto puede ser un echo/no-op si no se configura un handler especializado; una prueba de protocolo no equivale a demostrar que una tarea de negocio de ALBRA está implementada.

**Conclusión:** para integrar herramientas de un runtime externo, MCP con transporte real sería el contrato natural; para delegar tareas completas, A2A/HTTP o una API autenticada son alternativas. Falta implementar y aceptar una conexión real de extremo a extremo con AGENTE-LEADS. No se encontró evidencia en esta revisión de que esa conexión esté ya cerrada.

## 6. Dashboard, secretos y seguridad

### Evidencia favorable en código

- dashboard/ está ahora incluido en el repositorio.
- src/albra_seed/api/routes/provider_settings.py separa configuración no sensible del endpoint de escritura de clave y declara que la API key no se devuelve en lecturas.
- src/albra_seed/config/settings_store.py documenta cifrado Fernet en SQLite, clave maestra SETTINGS_ENCRYPTION_KEY suministrada desde entorno, fallo cerrado si falta o es inválida, y permisos restrictivos para archivo/directorio.
- La URL del proveedor NVIDIA está validada contra un host HTTPS permitido para evitar que la configuración se convierta en un proxy SSRF genérico.
- El workflow de dashboard incluye build, TypeScript y escaneo del bundle público para secretos; el check del HEAD terminó en éxito.

### Lo que sigue sin certificarse

Los checks no demuestran por sí solos configuración correcta en el despliegue real, separación/rotación de la clave maestra, backups y recuperación, TLS, exposición de red, CORS/origin real, comportamiento ante credenciales comprometidas ni aceptación completa de HITL. WEB_DASHBOARD_SECURITY.md conserva texto previo que afirma que el dashboard está fuera del repositorio; esa parte debe actualizarse para coincidir con el estado actual, sin retirar los controles que aún se deben probar en el entorno.

## 7. WhatsApp y reutilización de OpenClaw

### Hallazgo principal

dashboard/src/server/openclaw-adapted.js declara expresamente que adapta utilidades de la extensión WhatsApp de OpenClaw bajo licencia MIT, reescritas en JavaScript para el daemon de ALBRA. Las utilidades incluyen:

- Resolución de directorio persistente de sesión.
- Escritura atómica de credenciales, permisos restrictivos y backup/restauración.
- Reintentos con backoff exponencial y jitter.
- Watchdogs a nivel transporte y aplicación.
- Lock entre procesos para evitar múltiples propietarios de la misma sesión.
- Deduplicación persistente de mensajes.

THIRD_PARTY_NOTICES.md registra atribución y licencia MIT. Esto es reutilización selectiva de código/patrones, no una migración completa al runtime de OpenClaw. La atribución debe mantenerse si se conserva el código adaptado.

### Riesgo y límite funcional

El dashboard declara que el daemon utiliza Baileys, que opera como cliente no oficial de WhatsApp Web. No es lo mismo que WhatsApp Business Cloud API oficial. Persistencia y reconexión más robustas no eliminan las condiciones, límites o riesgos de la plataforma ni garantizan disponibilidad o ausencia de bloqueo.

Para producción comercial, se debe decidir explícitamente si se acepta ese riesgo o si se prioriza la API oficial de Meta. Esta revisión no verificó el estado real de emparejamiento, webhooks, credenciales, envío o recepción en el entorno de ALBRA.

## 8. Audio / ASR

docs/ASR_LIMITATION.md declara a fecha 2026-10-17 que la transcripción de audio no está operativa en producción por fallos de proveedores externos NVIDIA NVCF y bloqueo regional del fallback de Groq en el entorno descrito. Esa es una declaración del repositorio; no se reprodujeron las peticiones externas en esta auditoría. El código documenta fail-closed y la limitación debe mantenerse visible: el chat de texto puede funcionar mientras la transcripción de notas de voz está bloqueada.

## 9. Licencia y dependencia

- El proyecto AGENTE-GENERAL declara licencia MIT.
- La adaptación de OpenClaw también está atribuida bajo MIT en THIRD_PARTY_NOTICES.md.
- La licencia MIT permite uso/modificación/distribución sujeto a conservar avisos y condiciones; no elimina leyes aplicables, términos del canal WhatsApp ni obligaciones sobre datos personales.
- Esta conclusión de licencia no es asesoría jurídica ni sustituye una revisión de los avisos y versiones exactas de dependencias que se distribuyan.

## 10. Decisión recomendada para ALBRA

1. **No reemplazar íntegramente AGENTE-GENERAL en esta etapa.**
2. **No desplegar Hermes y OpenClaw como dos agentes centrales permanentes.** Eso contradice el objetivo de un único punto central y añade mantenimiento.
3. Mantener AGENTE-GENERAL como Seed/runtime propio mientras se cierra la brecha documental y se verifica el despliegue.
4. Aprovechar la adaptación selectiva de OpenClaw que ya existe, manteniendo avisos MIT y revisando su mantenimiento frente al upstream.
5. Si se necesita un único cerebro que coordine todos los agentes ALBRA, diseñar esa responsabilidad explícitamente sobre el Seed, sin mezclarla con el ADN reusable ni duplicar motores.
6. Evaluar Hermes únicamente como posible reemplazo del motor si una comparación equivalente demuestra menos mantenimiento y conserva controles. La documentación pública no demuestra por sí sola que Hermes sea mejor para el código concreto de ALBRA.
7. Priorizar las brechas reales: MCP remoto interoperable, conexión autenticada y auditada con AGENTE-LEADS, elección consciente de la vía WhatsApp, validación de ASR y aceptación de seguridad/HITL en staging.
8. Actualizar docs/CERTIFICATION.md, docs/WEB_DASHBOARD_SECURITY.md y la documentación de integración para que describan el HEAD actual.

## 11. Pendientes de aceptación

- [ ] Certificación canónica alineada con HEAD 7a71f01 y su evidencia CI.
- [ ] Prueba de instalación limpia reproducible; revisar fijación de dependencias/lockfiles.
- [ ] Pruebas de seguridad de provider settings y dashboard contra el despliegue real.
- [ ] Aceptación HITL real: permisos, persistencia, recuperación, backups y reconciliación de resultados externos inciertos.
- [ ] Decisión documentada: WhatsApp Baileys no oficial frente a WhatsApp Cloud API oficial.
- [ ] Prueba real de conversación y transcripción ASR cuando el proveedor esté disponible.
- [ ] Integración autenticada de AGENTE-LEADS con el punto central de ALBRA; probar denegación, timeout, duplicados, trazas y fallos.
- [ ] Comparación de Hermes con un único flujo y criterios idénticos antes de autorizar migración.

## 12. Fuentes primarias

- README: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/README.md
- Certificación: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/docs/CERTIFICATION.md
- Arquitectura: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/docs/ARCHITECTURE.md
- Seguridad: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/docs/SECURITY.md
- A2A integration: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/docs/A2A_INTEGRATION.md
- MCP server: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/src/albra_seed/mcp/server.py
- Provider settings API: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/src/albra_seed/api/routes/provider_settings.py
- Encrypted settings store: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/src/albra_seed/config/settings_store.py
- OpenClaw-adapted utilities: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/dashboard/src/server/openclaw-adapted.js
- Third-party notices: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/THIRD_PARTY_NOTICES.md
- ASR limitations: https://github.com/ALBRA8/AGENTE-GENERAL/blob/7a71f0126331345d884ed140fec079c95b3840f4/docs/ASR_LIMITATION.md
