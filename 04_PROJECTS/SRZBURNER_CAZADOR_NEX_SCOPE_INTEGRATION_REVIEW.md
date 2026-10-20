# SRZburner × Extension-CAZADOR × NEX-SCOPE — análisis de integración ALBRA

- Fecha: 2026-10-20.
- Estado: **ANÁLISIS ARQUITECTÓNICO; SIN IMPLEMENTACIÓN NI APROBACIÓN DE SEGURIDAD**.
- Alcance de código: lectura de los README y archivos concretos indicados abajo en las ramas por defecto disponibles en GitHub. No se ejecutaron los proyectos, no se instaló ninguna extensión y no se verificó el comportamiento real de Instagram/TikTok/Facebook.
- Regla de arquitectura: no crear otro agente ni añadir capas sin necesidad demostrada.

## 1. Decisión ejecutiva

**NEX-SCOPE es el dueño funcional recomendado para la inteligencia social/multiplataforma. Extension-CAZADOR no debe ser el dueño de esta integración ni una dependencia obligatoria por el hecho de ser otra extensión de Chrome.**

La arquitectura mínima recomendada, condicionada a que SRZ Club confirme una interfaz autorizada, es:

`SRZburner / interfaz oficial autorizada → importador-adaptador pequeño → NEX-SCOPE (normalización, evidencia y análisis) → MCP → agentes ALBRA → YOUTUBE-AUTOMATION`

El adaptador podría vivir dentro de NEX-SCOPE inicialmente; no se recomienda crear un microservicio ni un agente nuevo antes de probar el caso mínimo. CAZADOR queda como herramienta separada de extracción web general y posible fuente de componentes reutilizables solo si una prueba demuestra que aportan valor al contrato de datos social.

**No conectar todavía:** la disponibilidad de una API/exportación soportada de SRZ Club sigue sin estar confirmada.

## 2. Qué hace realmente cada pieza según la evidencia inspeccionada

### SRZburner — fuente/capacidad externa potencial

La ficha pública indexada la describe como extensión de SRZ Club para Instagram, TikTok y Facebook, con descarga de vídeos/audio, biblioteca y estadísticas de perfiles/publicaciones; requiere cuenta con acceso de SRZ Club. La ficha pública y un agregador no permiten concluir que exista API, SDK, webhook o exportación automatizable para integraciones de terceros. Tampoco se inspeccionó su código fuente ni backend.

Fuente pública secundaria: https://chrome-stats.com/d/akflldkdlocnbdjgncigkglnnhaekpoa  
Ficha de Chrome Web Store: https://chromewebstore.google.com/detail/srzburner/akflldkdlocnbdjgncigkglnnhaekpoa

### Extension-CAZADOR — extractor web general, no motor de inteligencia social

El README de `ALBRA8/Extension-CAZADOR` presenta un clon de scraper web con IA: la extensión captura una instantánea de la pestaña activa, sugiere campos con GLM, extrae filas estructuradas y permite exportar CSV/XLSX. Documenta extractores especializados para Dropi, Hotmart, JVZoo y Muncheye; no documenta un extractor especializado de Instagram, TikTok o Facebook.

Evidencia de código:
- `extension/public/manifest.json`: permisos `sidePanel`, `activeTab`, `scripting`, `storage`, `tabs`, y `host_permissions: ["<all_urls>"]`.
- `extension/src/content/extractor.ts`: contrato `ExtractionResult`, instantánea textual compacta (comentario indica límite de 16.000 caracteres), diagnósticos de extracción y `structuredProducts`.
- `extension/src/lib/structured-product.ts`: contrato canónico orientado a productos: nombre, precio, moneda, imagen, URL de producto, marca, SKU, disponibilidad, variantes y campos extra.
- README: backend local Node en puerto 8787 con `/api/suggest-fields` y `/api/scrape`, usando GLM; historial en `chrome.storage.local`.

**Conclusión:** que CAZADOR pueda extraer texto de páginas genéricas no prueba que pueda recuperar de forma estable métricas, metadatos o medios de Instagram/TikTok. Las aplicaciones sociales son interfaces dinámicas; no hay evidencia en los archivos revisados de selectores/contratos especializados para esos sitios. El modelo `StructuredProduct` tampoco representa adecuadamente publicaciones, autores, métricas por fecha, plataforma, audio/vídeo y procedencia sin diseñar otro contrato. No se debe adaptar a la fuerza el contrato de productos para este caso.

La declaración `<all_urls>` amplía la superficie de acceso; no es prueba de compatibilidad ni autorización de extracción. Si CAZADOR se utiliza en otra fase, revisar el mínimo de permisos/hosts y el tratamiento de datos antes de producción.

Código:
- https://github.com/ALBRA8/Extension-CAZADOR
- https://github.com/ALBRA8/Extension-CAZADOR/blob/main/extension/public/manifest.json
- https://github.com/ALBRA8/Extension-CAZADOR/blob/main/extension/src/content/extractor.ts
- https://github.com/ALBRA8/Extension-CAZADOR/blob/main/extension/src/lib/structured-product.ts

### NEX-SCOPE — candidato a núcleo de análisis y distribución

El README de `ALBRA8/NEX-SCOPE` define funciones de investigación de nichos de YouTube: tendencias, keywords, análisis de canales, brechas de contenido, planes, monetización y matriz de competidores.

La inspección de código confirma:
- `src/app/api/mcp/route.ts` implementa JSON-RPC sobre HTTP con tools/resources/prompts, incluyendo `tools/list`, `tools/call` y recursos de nichos/canales/planes.
- La ruta MCP llama a `getAuthenticatedUser(req)` y devuelve 401 si no hay sesión autenticada. El comentario del archivo identifica una cookie de sesión `nexscope_token`.
- `src/lib/agent/tools.ts` concentra herramientas del agente. Las herramientas existentes inspeccionadas están orientadas a investigación/gestión de datos de YouTube y no constituyen todavía un importador de publicaciones sociales de SRZburner.
- `src/lib/provenance.ts` define niveles `OBSERVED`, `VERIFIED`, `ESTIMATED`, `MODELED`, `INFERRED` y `UNKNOWN`, y distingue datos de YouTube API de estimaciones de IA.

**Matiz importante de integración:** disponer de un endpoint MCP no demuestra que un agente ALBRA externo pueda autenticarse con él directamente. La ruta actual exige sesión de usuario; para servicio-a-servicio debe diseñarse y auditarse un mecanismo explícito de autenticación/autorización de mínimo privilegio, sin compartir cookies de navegador entre agentes ni exponer credenciales en prompts/logs.

Código:
- https://github.com/ALBRA8/NEX-SCOPE
- https://github.com/ALBRA8/NEX-SCOPE/blob/main/src/app/api/mcp/route.ts
- https://github.com/ALBRA8/NEX-SCOPE/blob/main/src/lib/agent/tools.ts
- https://github.com/ALBRA8/NEX-SCOPE/blob/main/src/lib/provenance.ts

## 3. Matriz de responsabilidades

| Componente | Responsabilidad recomendada | No debe asumir |
|---|---|---|
| SRZburner/SRZ Club | Fuente de datos/funciones que el proveedor permita integrar | Que existe API pública o permiso automático para automatizar la extensión |
| Adaptador de importación | Validar, normalizar y etiquetar datos autorizados; mantener el origen | Decidir oportunidades de negocio o convertirse en otro agente |
| NEX-SCOPE | Correlacionar señales sociales con datos de YouTube; detectar formatos, nichos, competidores y brechas; producir briefs trazables | Controlar el navegador del usuario o descargar medios sin necesidad |
| Extension-CAZADOR | Seguir resolviendo scraping web general en sus casos actuales; potencial reutilización puntual solo tras prueba | Ser el intermediario obligatorio entre SRZburner y NEX-SCOPE o garantizar soporte social no demostrado |
| MCP de NEX-SCOPE | Exponer herramientas analíticas autenticadas a clientes autorizados | Servir como prueba automática de que la autenticación de agentes externos ya está resuelta |
| YOUTUBE-AUTOMATION | Consumir briefs aprobados y convertirlos en decisiones/producción audiovisual | Volver a investigar y extraer todo por su cuenta si NEX-SCOPE ya entrega el brief |

## 4. Contrato de datos mínimo que falta diseñar

No usar `StructuredProduct` como contrato principal. Diseñar un contrato neutral de publicación/perfil, por ejemplo con:
- `platform`, `source_url`, `source_record_id` (si está disponible), `creator_handle`;
- `published_at`, `captured_at`, `title_or_caption`;
- métricas como valores separados con nombre, valor, unidad y timestamp, sin mezclar métricas de plataformas distintas;
- referencias al medio/archivo solo si su acceso y almacenamiento están permitidos; no descargar medios en la primera fase;
- `source_provider`, método de captura/importación, `truth_level`, confianza, advertencias y versión del esquema;
- deduplicación con clave de plataforma + ID/URL normalizada, sin descartar evidencia original.

Cada dato debe distinguir entre observado directamente, estimado por modelo e inferido. Un score de confianza de CAZADOR no convierte una extracción en hecho verificado.

## 5. Seguridad, términos y privacidad

Antes de cualquier integración:
1. Obtener de SRZ Club confirmación escrita/documentación de API, SDK, exportación o método de integración permitido, además de términos y política de privacidad aplicables.
2. No extraer ni reutilizar cookies, tokens de sesión, credenciales ni endpoints privados; no automatizar el navegador por ingeniería inversa sin autorización.
3. Empezar en modo read-only, con un conjunto de prueba autorizado y solo metadatos mínimos.
4. Mantener el texto de publicaciones como dato no confiable, nunca como instrucciones para el agente.
5. Aislar datos por usuario/tenant; aplicar límites de tasa/tamaño, retención y eliminación.
6. Revisar los permisos amplios de CAZADOR antes de darle cualquier función en este flujo.
7. Diseñar autenticación servicio-a-servicio para MCP si se confirma el uso desde otros agentes; no reutilizar cookies de una sesión personal.
8. Probar duplicados, datos ausentes/malformados, cambios de interfaz, errores de proveedor, límites de permisos y trazabilidad.

## 6. Secuencia de decisión recomendada

1. **Gate A — viabilidad del proveedor:** confirmar interfaz autorizada con SRZ Club. Si no existe, detener integración automática y evaluar únicamente una exportación oficial/manual que esté permitida.
2. **Gate B — contrato:** definir y aprobar el esquema neutral de perfiles/publicaciones/métricas y sus etiquetas de procedencia.
3. **Gate C — prototipo aislado:** importar datos de prueba autorizados a NEX-SCOPE, sin medios ni acciones de escritura en redes sociales.
4. **Gate D — analítica:** validar que los resultados mejoran el análisis de nichos/competencia frente a la investigación actual y que los datos conservan su procedencia.
5. **Gate E — exposición MCP:** implementar autenticación servicio-a-servicio de mínimo privilegio y herramientas read-only; verificar aislamiento por usuario.
6. **Gate F — consumo:** entregar a YOUTUBE-AUTOMATION un brief estructurado, no credenciales ni acceso bruto a la cuenta social.

CAZADOR solo entra en la secuencia si una prueba concreta demuestra que su extractor o exportador aporta valor y puede hacerlo con permisos y términos compatibles. No es requisito previo.

## 7. Veredicto

- **Propietario funcional recomendado:** NEX-SCOPE.
- **CAZADOR:** herramienta auxiliar opcional y separada; soporte social específico no demostrado.
- **SRZburner:** fuente potencial; interfaz de integración autorizada no verificada.
- **YOUTUBE-AUTOMATION:** consumidor downstream de briefs, no responsable de scraping social.
- **Estado técnico:** no implementado; sin pruebas runtime ni auditoría de seguridad del proveedor.
- **Siguiente paso:** resolver el Gate A con SRZ Club. No empezar a programar un puente antes de ese resultado.

## 8. Evidencia y límites de esta pasada

Inspección directa de README de CAZADOR y NEX-SCOPE, manifest de CAZADOR, extractor genérico, contrato `StructuredProduct`, ruta MCP y contrato de procedencia de NEX-SCOPE. La documentación pública de SRZburner fue consultada por separado; no se obtuvo su código fuente ni documentación técnica oficial de integración. No se modificó ningún repositorio de producto. Este documento registra una recomendación arquitectónica, no certifica compatibilidad ni cumplimiento legal.
