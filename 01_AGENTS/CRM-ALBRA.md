# CRM ALBRA — FICHA DE CONTEXTO

- Repositorio: `ALBRA8/CRM-ALBRA`
- Estado: IMPLEMENTED CRM SYSTEM / VERIFIED DOCUMENTATION
- Tipo: CRM SaaS multi-tenant con agente IA.

## Capacidades documentadas

- Dashboard comercial.
- Clientes con timeline unificado.
- Oportunidades Kanban de 6 etapas.
- Productos/servicios e inventario.
- Calendario y Google Calendar.
- Cotizaciones.
- WhatsApp real mediante daemon Baileys.
- Automatizaciones trigger → condiciones → acciones, con ejecución durable.
- Chat AI con tools para clientes, oportunidades, tareas y consultas.
- Instagram y Telegram vía webhooks.
- Finanzas.
- Reportes PDF/Excel/CSV.
- Equipo, roles y permisos.
- Auditoría de actividad.
- Configuración multi-área.
- Búsqueda global y notificaciones push.

## Arquitectura

Next.js 16, React 19, TypeScript, Tailwind 4, shadcn/ui y Prisma/SQLite.

Multi-tenancy mediante `Organization` + `organizationId` en la capa de datos.

Seguridad documentada:

- Bearer JWT.
- rate limiting.
- HMAC en webhooks.
- cifrado de credenciales por organización.
- daemon WhatsApp interno, no público.

## Relación con otros agentes

- AGENTE-LEADS tiene hooks de CRM-ALBRA documentados, pero el README de Leads aclara que el hook es integración opcional y no convierte Leads en CRM completo.
- CRM es un sistema comercial transversal, no un sustituto del agente Leads.

## Auditoría / pendientes

El repositorio actual necesita ficha canónica completa de decisiones, capacidades verificadas, estado histórico y auditorías independientes en CONTEXT. Esta ficha es documentación de la fuente, no certificación independiente.
