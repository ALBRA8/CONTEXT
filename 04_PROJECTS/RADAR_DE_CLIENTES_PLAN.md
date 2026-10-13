# P-0009 — RADAR DE CLIENTES (evolución de AGENTE-LEADS)

- Estado: AUTORIZADO POR PROPIETARIO (2026-10-13) — ver D-0015. Ejecución en curso.
- Repositorio de trabajo: `ALBRA8/AGENTE-LEADS` (rama `radar-clientes/p0-security`)
- Origen: PROMPT MAESTRO de corrección y evolución + auditoría `03_AUDITS/AGENTE_LEADS_RADAR_AUDIT_2026-10-13.md`
- Principio: auditar y corregir lo existente; no reconstruir. El bot raíz ya resuelve fallback/dedup/evidencias; el dashboard tiene la estructura UI completa.

## Alcance autorizado

### P0 — Seguridad e integridad de datos (en ejecución)
1. Endpoint `seed-demo`: sin exposición pública de credenciales, gated por env, sin contraseña en respuestas, usuario demo `role:"user"`.
2. Signup: `role:"user"` (cerrar escalada de privilegios).
3. Scoring comercial real: usar las señales comerciales existentes (tier, nicheFit, pain, techStack, freshness), determinista, pesos documentados, breakdown persistido, `research_state` real.
4. Extracción completa de teléfono/email (no truncada) con normalización.
5. 22 errores de TypeScript de `overpass-adapter.ts` corregidos; `ignoreBuildErrors` eliminado del build.
6. Módulo SSRF compartido en dashboard aplicado a todos los fetchers server-side (freshness-checker, tech-stack-detector, web-scraper).
7. Tests unitarios para cada corrección + lint operativo.

### P1 — Fiabilidad de datos (siguiente fase)
Places completo (place_id, coordenadas, rating, businessStatus, paginación nextPageToken, timeout, clasificación de errores), fallback entre proveedores con envelope, dedup multi-señal, keywords en textQuery, modelo de evidencia por campo en Prisma, clasificación de presencia digital (4 estados), marcado estructural DEMO/MOCK, trazabilidad de coste/llamadas.

### P2 — Inteligencia comercial
Auditoría PageSpeed Insights v5 (estados explícitos OK/UNREACHABLE/TIMEOUT/INVALID_URL), resultados persistidos con fecha y URL evaluada, taxonomía FACT/INFERENCE/HYPOTHESIS/RECOMMENDATION en intelligence, determinización de pain/tier antes del LLM.

### P3 — Experiencia Radar de Clientes
Exportación CSV real (permisos+filtros), ficha imprimible/PDF verificable, borradores de primer contacto por canal (llamada/WhatsApp/email) bajo control del usuario sin auto-envío, Radar de Nichos con estadísticas observadas vs estimaciones, ficha con `GET /api/prospects/[id]`, mejoras de ajustes y glosario ES.

## Restricciones de ejecución (condiciones del propietario)

- Sin despliegues a producción; sin envíos comerciales reales; sin migraciones destructivas; sin operaciones de pago no autorizadas; sin credenciales reales en informes/logs.
- Pruebas de APIs externas con coste: solo con autorización y configuración explícitas.
- Constructor como único responsable de integración; agentes especializados con propiedad de archivos delimitada; contratos compartidos documentados antes de implementar.
- Ningún frente se declara terminado sin evidencia verificable (tests, typecheck, build).

## Contratos compartidos definidos

1. Prospecto normalizado: modelo `Prospect` Prisma ampliado con placeId/lat/lng/rating/ratingCount/businessStatus (aditivo, migración reversible).
2. Evidencias: tabla `Evidence(prospectId, field, value, status, source, retrievedAt, confidence)` — espejo del EvidenceRecord del bot raíz.
3. Presencia digital: enum `digitalPresence` = WEB_PROPIA_CONFIRMADA | SOLO_RED_SOCIAL_O_DIRECTORIO | SIN_WEB_CONFIRMADA | WEB_PENDIENTE_DE_VERIFICAR.
4. Scoring comercial: determinista, pesos documentados, salida `{score, breakdown[{field, points, reason}]}`.
5. PageSpeed: `{url, strategy, scores{performance,seo,accessibility,bestPractices}, auditedAt, status}`.
6. Estados de búsqueda: SearchJob con `status` real (completed|partial|failed) y conteo de fuentes fallidas.
7. Errores de proveedor: envelope `{ok, data, error{code}}` con taxonomía AUTH/UNAVAILABLE/RATE_LIMIT/EMPTY (patrón del bot raíz).
8. Secretos: cifrado en reposo AES-256-GCM + enmascaramiento en GET (mantener patrón actual).
9. Exportación: CSV `text/csv` BOM UTF-8 respetando filtros y permisos; PDF solo si hay experiencia de exportación verificable.
10. Datos DEMO: flag estructural `isMock` + bloqueo de outreach sobre mocks; `sourceUrl` null en mocks.
