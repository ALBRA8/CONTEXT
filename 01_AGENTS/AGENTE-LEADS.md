# AGENTE-LEADS — RECUPERACIÓN DIRIGIDA WAVE 3

- Repositorio: `ALBRA8/AGENTE-LEADS`
- Clasificación: agente de lead intelligence / prospección B2B.
- Estado documental actual: V2 stable según README vigente.

## Propósito

Agente conversacional de Telegram para prospección B2B. Recibe solicitudes en lenguaje natural, descubre candidatos, investiga, valida contra fuentes externas, deduplica, puntúa, genera inteligencia con LLM y puede preparar/enviar outreach por APIs oficiales.

Regla de evidencia central: cada dato del reporte queda etiquetado como observado, validado, no encontrado o inferido. Regla P0.6: `no encontrado` ≠ `no tiene`.

## Arquitectura actual documentada

Capas:

1. Telegram: `grammy`, long polling, whitelist y comandos.
2. LLM/Agent: GLM 5.3 Flash vía NVIDIA NIM, system prompt en español y tool-calling con máximo de 10 iteraciones.
3. Pipelines: `DISCOVERY → RESEARCH → VALIDATION → EVIDENCE → DEDUP → SCORING → INTELLIGENCE → STORAGE → REPORT → CRM`.
4. Providers/infra: Apify/Mock discovery, Google Search, RapidEmail/Fly.io, Scrapling/Python, SendGrid y WhatsApp Cloud API.
5. Storage: dos SQLite WAL, conversaciones y lead intelligence.
6. Report: Markdown con estado de evidencia por campo.

## Capacidad actual documentada

- `run_lead_pipeline`: pipeline determinístico completo.
- `run_outreach`: propuestas personalizadas y outreach opcional.
- Legacy tools: `scrape_instagram_leads`, `enrich_lead_profile`, `verify_email`, `save_lead`, `scrape_stealth`, `get_current_time`.
- Discovery, research, validation, evidence, dedup multi-señal, scoring 0–100 e intelligence LLM.
- ExecutionTrace por pipeline.
- CRM-ALBRA webhook opcional; el README aclara que no es el CRM completo.
- Outreach por APIs oficiales y `dry_run=true` por defecto.

## Historial de auditoría recuperado

`AUDITORIA_HERMES.md` registra una auditoría histórica de 2026-04-10 con 6.5/10 y estado funcional pero incompleto para producción. Entre sus problemas históricos estaban dependencia de APIFY, scraping limitado, verificación de email incompleta, enriquecimiento limitado, pocas tools y ausencia de outreach/analytics.

La arquitectura vigente del README V2 ya es sustancialmente más amplia: pipeline determinístico, evidence engine, scoring, intelligence, outreach y CRM hooks. Por lo tanto, la auditoría de abril debe conservarse como **estado histórico**, no como descripción del estado actual.

## Límites actuales documentados

- Telegram `/help` y `/stats` figuran como planned/no implementados.
- Voz: el handler descarga notas de voz pero Whisper no estaba implementado en el README vigente.
- APIFY es opcional; sin token existe `MockDiscovery`.
- CRM-ALBRA hook es opcional y no equivale a tener CRM embebido.
- Las tools legacy se mantienen por compatibilidad, pero los flujos nuevos prefieren `run_lead_pipeline` y `run_outreach`.

## Evidencia / estado

**VERIFIED FROM CURRENT README:** arquitectura, pipeline, tools, evidencia, scoring, outreach, storage y límites descritos arriba.

**VERIFIED HISTORICAL DOCUMENTATION:** auditoría Hermes 2026-04-10 y sus hallazgos.

**NOT_VERIFIED HERE:** auditoría independiente actual del HEAD y ejecución fresh clean-room en esta Wave 3.

## Relación con otros agentes

- Chismoso: investigación/intelligence; puede descubrir señales externas, pero no debe absorber automáticamente la especialidad de Leads.
- Krea: productización; no es sustituto de prospección.
- CRM-ALBRA: sistema comercial separado; Leads puede integrarse mediante hook.
- Despacho Jurídico: no asumir que Leads realiza legal/rights clearance.
