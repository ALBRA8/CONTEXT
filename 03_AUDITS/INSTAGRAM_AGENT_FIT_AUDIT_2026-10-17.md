# Auditoría de encaje: Instagram, AGENTE-LEADS y AGENTE-CHISMOSO

- Fecha: 2026-10-17
- Tipo: revisión documental/técnica de solo lectura y decisión de encaje recomendada.
- Estado: hallazgos registrados; no es una implementación ni una nueva autorización para modificar agentes.
- Alcance de escritura: solo `ALBRA8/CONTEXT`.
- Repositorios de agentes: no modificados.
- Pruebas: esta revisión no ejecutó suites de test ni volvió a certificar el HEAD actual. Los resultados históricos se citan como históricos, no como evidencia nueva.

## Decisión de encaje

**La capacidad de descubrimiento y enriquecimiento de contactos debe vivir en AGENTE-LEADS, dentro de Radar de Clientes.** No crear un agente nuevo ni duplicar el pipeline de Leads.

**AGENTE-CHISMOSO debe seguir como fuente upstream de inteligencia de mercado:** detectar tendencias/señales, agrupar problemas y proponer oportunidades con evidencia; cuando una oportunidad requiera prospectar o enriquecer contactos, derivarla a AGENTE-LEADS. La arquitectura de Chismoso contempla `suggestedNextAgent: "AGENTE-LEADS"`.

**AGENTE-GENERAL no debe absorber esta función especializada.** Mantener su papel de infraestructura/runtime/seguridad y respetar el freeze de Seed.

## Evidencia interna revisada

### AGENTE-CHISMOSO
- README: https://github.com/ALBRA8/AGENTE-CHISMOSO/blob/main/chismoso/README.md
- Arquitectura: https://github.com/ALBRA8/AGENTE-CHISMOSO/blob/main/chismoso/docs/ARCHITECTURE.md
- Definition of Done: https://github.com/ALBRA8/AGENTE-CHISMOSO/blob/main/chismoso/docs/DEFINITION_OF_DONE.md
- Misión identificada: investigación de mercado, tendencias/señales, agrupación de problemas y oportunidades trazables. No es el scraper genérico ni el dueño del CRM de prospectos.
- El DoD contiene una referencia histórica de 41 tests aprobados en la auditoría documentada; no se repitieron aquí.

### AGENTE-LEADS
- README: https://github.com/ALBRA8/AGENTE-LEADS/blob/main/README.md
- Evidencia: https://github.com/ALBRA8/AGENTE-LEADS/blob/main/src/agent/core/evidence.ts
- Registro de proveedores: https://github.com/ALBRA8/AGENTE-LEADS/blob/main/src/agent/providers/registry.ts
- Herramienta heredada de Instagram: https://github.com/ALBRA8/AGENTE-LEADS/blob/main/src/agent/tools/scrape_instagram_leads.ts
- Encaje funcional: descubrimiento, investigación y validación de prospectos; evidencia por campo; deduplicación, scoring, persistencia e informes.
- La herramienta heredada `scrape_instagram_leads` usa búsqueda de Google a través de Apify con consultas del tipo `site:instagram.com` y `@gmail.com`, con un máximo documentado de 10 resultados. **No equivale a enumerar seguidores de una cuenta competidora.**
- La extracción actual por búsqueda indexada no garantiza cobertura completa, emails válidos ni datos de seguidores.
- Punto de mejora a verificar en el código actual: evidencia de email/teléfono con URL exacta por campo y procedencia verificable; no basta con una etiqueta genérica del proveedor.
- Riesgo operativo a comprobar: credenciales de Apify en parámetros de URL pueden filtrarse en logs/proxies si no se redactan.
- La auditoría histórica de Radar de Clientes del 2026-10-13 es una fotografía de `AGENTE-LEADS @ e73d15b`; no usarla como estado actual sin comparar HEAD y cambios posteriores: https://github.com/ALBRA8/CONTEXT/blob/main/03_AUDITS/AGENTE_LEADS_RADAR_AUDIT_2026-10-13.md
- Plan Radar: https://github.com/ALBRA8/CONTEXT/blob/main/04_PROJECTS/RADAR_DE_CLIENTES_PLAN.md

### AGENTE-GENERAL
- Referencia arquitectónica: https://github.com/ALBRA8/CONTEXT/blob/main/00_MASTER_CONTEXT/ALBRA_ARCHITECTURE.md
- Mantener como infraestructura y controles compartidos; no ubicar allí el pipeline de prospección. Seed permanece congelado.

## Referencias externas examinadas como opciones, no como aval de calidad

- Instaloader: https://github.com/instaloader/instaloader — referencia de código para interacción con perfiles/publicaciones y estructuras relacionadas, sujeta a acceso real, límites y cambios de plataforma.
- Scrapling: https://github.com/D4Vinci/Scrapling — extracción genérica de páginas; no es por sí sola un enumerador de seguidores. Ya se ha identificado como componente de Leads.
- IG Checker: https://github.com/curetcore/ig-checker — análisis local de exportaciones oficiales de la cuenta del propio usuario; no equivale a extraer arbitrariamente seguidores de competidores.
- Lead Search Agent: https://github.com/kandarpa02/lead_agent — patrones de arquitectura de prospección, no prueba de calidad de datos.
- Lead-Reach: https://github.com/getleads-humain/Lead-Reach — referencia de arquitectura de enriquecimiento multicanal; calidad no verificada.
- Apify Instagram Followers Scraper: https://apify.com/publicsignallabs/instagram-followers — el proveedor anuncia extracción/exportación y precios; las capacidades, cobertura, coste y términos deben revalidarse antes de contratar.
- Apify Instagram Profile Scraper: https://apify.com/afanasenko/instagram-profile-scraper — opción anunciada para perfiles/enriquecimiento; no se validó de forma independiente.
- Apify Followers Scraper (no login): https://apify.com/data-slayer/instagram-followers-scraper---no-login — cifras y modalidades son declaraciones del proveedor, no benchmark independiente.
- PhantomBuster: https://phantombuster.com/pricing y TexAu: https://texau.com/pricing — automatización/enriquecimiento general; no asumir extracción completa ni cumplimiento por el solo hecho de que exista un producto.
- API de Instagram de Meta: https://www.postman.com/meta/instagram/documentation/6yqw8pt/instagram-api — no debe suponerse que ofrece la lista completa de seguidores de cualquier cuenta competidora.
- Términos de Custom Audiences de Meta: https://www.facebook.com/legal/terms/customaudience/update — antes de usar datos para publicidad, confirmar derechos, permisos y base legal aplicable. El hash no convierte una recopilación no autorizada en autorizada.

## Diseño recomendado para AGENTE-LEADS

Separar dos resultados que no deben confundirse:

1. **Business Contact Enrichment:** contactos profesionales publicados por el negocio, sitio oficial enlazado, página/formulario de contacto y fuentes con permisos compatibles. Guardar fuente exacta por campo, fecha de consulta, método/proveedor, estado, confianza y clasificación de permisos.
2. **Audience Intelligence:** métricas e insights agregados y etiquetados; no construir por defecto listas masivas de seguidores individuales ni intentar reconstruir identidades si la fuente solo permite estadísticas agregadas.

Reutilizar el contrato de evidencia y la cadena de proveedores de Leads si el HEAD actual confirma que siguen siendo adecuados. Implementar proveedores intercambiables tras comparar cobertura, tasa de email válido, duplicados, coste por resultado útil, latencia, fallos, estabilidad y condiciones de uso.

## Límites de seguridad y cumplimiento

- No eludir autenticación, controles de acceso, rate limits ni defensas anti-bot.
- No realizar scraping masivo de datos personales de seguidores como comportamiento predeterminado.
- No inferir emails personales por patrones ni presentar inferencias como hechos.
- No automatizar mensajes ni cargar audiencias en Meta Ads.
- Exigir revisión humana y validación de términos/base legal antes de habilitar una fuente.
- Minimizar datos, limitar retención/acceso y registrar trazabilidad.
- Proteger tokens de proveedores: evitar secretos en query strings cuando sea posible y redactarlos de logs, errores y telemetría.
- Aplicar controles SSRF/egress a cualquier fetch de URLs suministradas por usuarios.

## Siguiente trabajo técnico (todavía no ejecutado)

1. Comparar HEAD actual de AGENTE-LEADS con la auditoría histórica y el plan Radar; confirmar qué P0/P1/P2 siguen pendientes y no asumir que el plan equivale al código.
2. Revisar el estado actual de `scrape_instagram_leads`, registro de proveedores, evidencia, SSRF/egress, secretos y tests.
3. Hacer una evaluación de proveedores con un conjunto de prueba permitido y pequeño; registrar métricas reproducibles antes de elegir uno.
4. Proponer una implementación por etapas y pruebas de aceptación dentro de AGENTE-LEADS, sin tocar otros repositorios ni desplegar/comprar/ejecutar scraping real sin autorización explícita.

## Estado final

- Recomendación de arquitectura: AGENTE-LEADS.
- Rol de AGENTE-CHISMOSO: inteligencia upstream y derivación de oportunidades a Leads.
- AGENTE-GENERAL: infraestructura compartida; no dueño de esta capacidad.
- Implementación: no realizada.
- Benchmark independiente de proveedores: no realizado.
- Cumplimiento legal/plataforma para una ejecución concreta: requiere revisión previa.
