# SRZburner × NEX-SCOPE — Evaluación de encaje arquitectónico

- Fecha: 2026-10-20.
- Estado: **PROPUESTA DE INTEGRACIÓN; NO IMPLEMENTADA**.
- Repositorio fuente inspeccionado: `ALBRA8/NEX-SCOPE`, rama por defecto `main`, HEAD observado `09f0cb18d259fc1ef6be007be61338b4350807ee` (2026-10-07).
- Extensión referenciada: [SRZburner en Chrome Web Store](https://chromewebstore.google.com/detail/srzburner/akflldkdlocnbdjgncigkglnnhaekpoa).
- Alcance de la inspección de SRZburner: ficha pública y metadatos de terceros; no se inspeccionó su código fuente ni su backend privado.

## 1. Qué parece aportar SRZburner

La ficha pública indexada describe una extensión de SRZ Club para investigar contenido en Instagram, TikTok y Facebook:
- descargar vídeos o audio de Reels/TikTok, individualmente o en secuencia desde perfiles;
- recortar vídeos antes de descargarlos;
- guardar reels y cuentas en una biblioteca organizada por carpetas y sincronizada con una cuenta SRZ Club;
- consultar estadísticas de perfiles, fecha de publicación, ritmo de publicación y una calculadora de conversión por millón de vistas.
- requiere una cuenta de SRZ Club con acceso a la extensión.

Fuente de apoyo para esta descripción: [ficha indexada de SRZburner en Chrome-Stats](https://chrome-stats.com/d/akflldkdlocnbdjgncigkglnnhaekpoa), consultada en esta pasada. Es un agregador de terceros, no una auditoría del proveedor.

El objetivo de ALBRA aquí NO es instalarla y usarla manualmente como herramienta separada. La idea a evaluar es integrar sus capacidades/datos en el flujo de investigación de ALBRA, siempre que exista una interfaz técnica y un permiso contractual legítimos.

## 2. Por qué NEX-SCOPE es un candidato fuerte

La revisión de `ALBRA8/NEX-SCOPE` confirma que el repo ya tiene una base de integración apropiada:

- El README define NEX-SCOPE como plataforma de investigación de nichos de YouTube: tendencias, keywords, análisis de canales, brechas de contenido, planes de contenido, monetización y matriz de competidores.
- Existe `src/app/api/mcp/route.ts`: endpoint MCP por JSON-RPC/HTTP que expone tools, resources y prompts a clientes/agentes externos. El comentario del archivo declara autenticación mediante cookie de sesión `nexscope_token`.
- Existe `src/lib/agent/tools.ts`: registro y ejecución centralizada de herramientas del agente.
- Existe `src/app/api/agent/route.ts` y `src/lib/agent/executor.ts`: ejecución de agente con streaming y trazas.
- Existe `src/lib/provenance.ts`: contrato de procedencia y nivel de verdad (OBSERVED, VERIFIED, ESTIMATED, MODELED, INFERRED, UNKNOWN), útil para no presentar estimaciones sociales como hechos oficiales.
- El esquema descrito en README ya contempla nichos, canales, planes, historial y ajustes persistentes por usuario.

Referencias directas:
- [NEX-SCOPE README](https://github.com/ALBRA8/NEX-SCOPE/blob/main/README.md)
- [MCP endpoint](https://github.com/ALBRA8/NEX-SCOPE/blob/main/src/app/api/mcp/route.ts)
- [Tool registry](https://github.com/ALBRA8/NEX-SCOPE/blob/main/src/lib/agent/tools.ts)
- [Provenance contract](https://github.com/ALBRA8/NEX-SCOPE/blob/main/src/lib/provenance.ts)

**Evaluación:** NEX-SCOPE es un candidato natural para ser el *consumidor, normalizador y analista* de señales multiplataforma. No hace falta crear otro agente para esto antes de probar la integración.

## 3. Encaje recomendado en ALBRA

Separar funciones en lugar de convertir la extensión en un agente:

1. **SRZburner / SRZ Club — fuente externa potencial.** Proporcionaría únicamente los datos o acciones que el proveedor permita exportar/acceder legal y técnicamente. No asumir que existe una API pública.
2. **Adaptador SRZ dentro del límite de integración de NEX-SCOPE — capa por construir si la viabilidad se confirma.** Convierte datos autorizados a un contrato neutral, valida campos y normaliza identificadores, fechas, plataforma y métricas.
3. **NEX-SCOPE — núcleo analítico.** Une señales de YouTube con señales sociales de Instagram/TikTok/Facebook para detectar formatos en crecimiento, cadencia, cuentas competidoras, brechas y oportunidades de nicho. Cada dato conserva fuente, fecha de captura y nivel de verdad.
4. **MCP de NEX-SCOPE — puerta para otros agentes ALBRA.** Expone las capacidades analíticas mediante herramientas MCP con autenticación y alcance por usuario; no da acceso indiscriminado a perfiles ni sesiones del navegador.
5. **YOUTUBE-AUTOMATION — consumidor downstream opcional.** Recibe briefs/ideas/formatos ya analizados para decidir qué producir; no debería encargarse de investigar o extraer datos sociales si NEX-SCOPE puede resolver esa especialidad.
6. **AGENTE-GENERAL / controles ALBRA — gobernanza.** Define permisos, límites de acceso, trazabilidad y política de uso de datos. No reemplaza el adaptador técnico.

Flujo conceptual:

`SRZburner/SRZ Club (si hay acceso soportado) → adaptador y normalización → NEX-SCOPE (evidencia + análisis) → MCP → agentes ALBRA / YOUTUBE-AUTOMATION`

No se propone incrustar el código propietario de SRZburner en NEX-SCOPE, clonar la extensión ni automatizar acciones privadas sin autorización. Si no existe API/exportación autorizada, primero se debe pedir al proveedor documentación, formatos de exportación o acceso de desarrollador. Un puente local de navegador sería una alternativa separada y condicionada a permisos/contratos; no debe suponerse disponible.

## 4. Casos de uso que sí justificarían la integración

- **Radar de formatos multiplataforma:** detectar temas/formatos que aparecen en TikTok/Reels antes de que se saturen en YouTube.
- **Benchmark de competidores:** comparar cuentas/canales, frecuencia de publicación, fechas y señales de rendimiento sin mezclar métricas no equivalentes.
- **Biblioteca de evidencia:** guardar referencias a publicaciones y sus metadatos, con URL/origen/fecha y usuario que las importó.
- **Brief para producción:** transformar hallazgos verificados en un brief que YOUTUBE-AUTOMATION pueda convertir después en una especificación de producción.
- **Seguimiento de nichos:** asociar observaciones sociales a un nicho guardado en NEX-SCOPE y volver a medirlas con el tiempo.

La descarga de medios no debería ser el centro de esta integración. El valor principal para ALBRA sería la inteligencia competitiva y de formatos; cualquier almacenamiento/reutilización de medios exige comprobar derechos, términos de plataforma y permisos de la cuenta.

## 5. Seguridad y límites detectados

La ficha indexada de terceros informa que SRZburner solicita permisos de Chrome relacionados con descargas, almacenamiento, identidad, offscreen, declarativeNetRequest con acceso a hosts, scripting y activeTab; también enumera acceso a Instagram y permisos opcionales de Facebook. El mismo agregador marca riesgo elevado, en parte porque la extensión es reciente. Esto no demuestra que sea maliciosa, pero sí justifica revisión antes de darle acceso a perfiles/sesiones sensibles.

Controles mínimos antes de conectar:
- confirmar quién opera SRZ Club, términos, política de privacidad, retención y eliminación de datos;
- obtener lista exacta de permisos y dominios de la versión instalada, además de documentación oficial de integración;
- no exportar cookies, contraseñas, tokens de sesión ni datos de cuenta no necesarios;
- credenciales de proveedor solo en backend/secret store, nunca en prompts ni logs;
- datos separados por usuario/tenant; allowlist de plataformas y URLs; límites de tamaño y tasa;
- registrar procedencia, timestamp, plataforma, URL fuente y si cada campo es observado o estimado;
- tratar texto capturado de páginas como dato no confiable, nunca como instrucciones para el agente;
- comenzar en modo read-only/importación de metadatos; dejar descarga/recorte fuera del primer MVP;
- pruebas de aislamiento multiusuario, redacción de secretos, duplicados, datos malformados y fallos del proveedor.

## 6. Decisión provisional

**Candidato recomendado: NEX-SCOPE.** Confianza arquitectónica: alta, porque ya tiene agente, registro de tools, endpoint MCP y contrato de procedencia. Confianza de viabilidad técnica con SRZburner: **pendiente**, porque la ficha pública revisada no prueba la existencia de API, SDK, webhook o exportación automatizable.

No implementar aún. El siguiente gate es obtener evidencia oficial de la interfaz que SRZ Club ofrece a terceros y revisar los términos. Después, definir un contrato de datos pequeño y hacer un prototipo read-only con datos de prueba. Si no hay interfaz permitida, decidir entre una exportación manual soportada o descartar la integración; no inventar una API ni acoplar la extensión por ingeniería inversa sin autorización.

## 7. Evidencia y límites de esta pasada

- Inspección de código de NEX-SCOPE: lectura de README, `src/app/api/mcp/route.ts`, `src/app/api/agent/route.ts`, `src/lib/agent/tools.ts`, `src/lib/agent/executor.ts` y `src/lib/provenance.ts`.
- Inspección de SRZburner: ficha pública enlazada y resultado indexado por agregador; no se obtuvo código fuente, API docs ni condiciones contractuales completas.
- No se modificó `ALBRA8/NEX-SCOPE` ni se implementó ninguna integración. Esta ficha es una evaluación arquitectónica, no una aprobación de seguridad del proveedor.
