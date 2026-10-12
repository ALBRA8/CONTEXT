# AGENTE CHISMOSO — FICHA DE CONTEXTO

- Repositorio: `ALBRA8/AGENTE-CHISMOSO`
- Estado de la ficha: UPDATED WAVE 3 / VERIFIED FROM REPOSITORY DOCUMENTATION
- Clasificación: agente de investigación / inteligencia.
- Versión documentada: CHISMOSO V1.0.

## Propósito

CHISMOSO convierte un objetivo de alto nivel en un reporte estructurado de señales, tendencias, problemas y oportunidades de negocio. Su arquitectura documentada establece que el cerebro es determinista: el LLM propone y los engines deciden. Las afirmaciones deben llevar `TruthLevel` y evidencia trazable a una fuente real.

## Arquitectura verificada por `chismoso/docs/ARCHITECTURE.md`

Cinco capas con dependencias unidireccionales:

1. Interfaz: `src/cli.ts` — parseo de argumentos, wiring y salida.
2. Orchestration: planner, LLM, tools, orchestrator y reporter.
3. Intelligence: normalizer, clustering, cross-source, trends, problems y opportunities.
4. Providers: web search, Reddit/communities, web content y Google Trends.
5. Foundation: models, errors, logger, DB, repositories y config.

Propiedades importantes:

- Providers desacoplados de intelligence/orchestration.
- Engines de intelligence diseñados como funciones puras.
- Persistencia separada en repositories/DB.
- Dependencias externas detrás de adapters/providers.

## Providers y verdad de datos

Documentación de arquitectura/DoD identifica:

- `web_search`: provider real.
- `reddit_communities`: búsqueda vía web search restringida a Reddit/Quora.
- `web_content`: lectura de páginas mediante `page_reader`.
- `google_trends`: **UNAVAILABLE**; devuelve vacío con `PROVIDER_UNAVAILABLE`, no simula datos.

El documento `DEFINITION_OF_DONE.md` afirma explícitamente que los 3 providers activos usan llamadas reales al `z-ai-web-dev-sdk` y que Google Trends no se presenta como disponible.

## Intelligence engines

- Normalización `RawItem → Signal + Evidence`.
- Clustering por overlap coefficient.
- Cross-source confidence.
- Trend detection con growth, persistence, cross-source, recency y velocity.
- Problem detection con umbral de señales de fricción distintas.
- Opportunity scoring con factores explícitos y penalizaciones por competition/uncertainty.
- Report engine genera Markdown y `IntelligenceReport` JSON.

La arquitectura documenta 9 tablas SQLite principales para signals, evidence, trends, problems, opportunities, investigations, provider runs, topics y topic observations.

## Orquestación

El `Orchestrator` mantiene un loop acotado por presupuesto, usa `ResearchPlanner`, ToolRegistry y mapeo provider→tool. La investigación termina en un reporte estructurado y persistible.

## MCP

El repositorio contiene `docs/MCP.md` y ejemplos de clientes MCP, por lo que MCP está documentado como parte del organismo. No se infiere aquí una lista de servidores externos no verificada.

## Auditoría / Definition of Done

`chismoso/docs/DEFINITION_OF_DONE.md` registra una verificación independiente ejecutada el 2026-10-05:

- 41/41 tests PASS en 7 archivos.
- 39 tests unitarios + integración incluida en providers/DB + 2 E2E.
- Investigación canónica completó el pipeline completo desde investigation hasta actionable report.
- Verificación anti-ficción: ningún provider simula datos.
- Google Trends marcado UNAVAILABLE.

Hallazgos menores de esa auditoría histórica:

1. `executiveSummary` podía mostrar `RUNNING` aunque la investigación ya hubiera terminado.
2. Algunos `Signal.timestamp` podían no ser ISO estricto.
3. Un E2E de evidencia insuficiente era permisivo con varios estados.
4. `better-sqlite3` podía producir `Aborted (core dumped)` al teardown sin afectar los tests verdes.

Estos hallazgos deben conservarse como historial; no se afirma aquí que hayan sido corregidos en HEAD actual.

## Límites

- Google Trends no está implementado como provider real en V1.0.
- No se debe asumir scraping propio: la arquitectura delega en providers externos.
- No se debe asumir capacidad jurídica, de copyright, CRM o de outreach solo porque el agente hace research.
- La oportunidad generada puede proponer `AGENTE-LEADS` como siguiente agente, según el documento de arquitectura.

## Evolución histórica

La conversación inicialmente describió a Chismoso como un agente que “busca cosas” y candidato para investigación externa. Wave 3 confirma esa caracterización con evidencia directa de arquitectura, providers, engines, persistencia y DoD.

## Estado de evidencia

**VERIFIED FROM REPOSITORY:** arquitectura, providers, intelligence engines, persistencia, MCP documentado y DoD histórico.

**HISTORICAL:** auditoría 2026-10-05 y sus hallazgos.

**NOT_VERIFIED:** estado exacto del HEAD actual respecto de todos los hallazgos históricos; no se ejecuta una auditoría nueva completa en esta Wave 3.
