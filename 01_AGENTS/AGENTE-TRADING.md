# AGENTE TRADING — FICHA DE CONTEXTO

- Repositorio: `ALBRA8/AGENTE-TRADING`
- Estado: VERIFIED DOCUMENTATION / IMPLEMENTED REPOSITORY
- Especialidad: análisis y operativa de trading institucional basada en Smart Money Concepts (SMC).

## Misión

Agente de IA para análisis y operativa institucional con integración a MetaTrader 5 mediante bridge local Python y control/interacción por Telegram.

## Capacidades documentadas

- Estructura top-down H1/M15/M5/M1.
- POI, Breaker Blocks, Order Blocks, FVG/imbalances.
- BOS y ChoCh.
- Killzones de Londres, Nueva York y Asia, con foco especial en US30 y Forex.
- Gestión de riesgo por operación y cálculo dinámico de lotaje/Stop Loss.
- Filtro de noticias/macroeconomía mediante Tavily.
- Memoria/estado SQLite.
- Track Record auditable de decisiones, incluyendo razones de WAIT/REVIEW.
- Bot Telegram para control, alertas, gráficos y confirmaciones.
- Bridge local Flask para MT5; la ejecución de órdenes pasa por el bridge local.

## Arquitectura de modelos

El README documenta dos tiers NVIDIA NIM:

- `deep`: research manager, trade planner y gate final.
- `quick`: analistas, debate, riesgo y reflexión.

## Seguridad y autonomía

- DEMO es el modo por defecto y no coloca órdenes.
- LIVE requiere opt-in explícito mediante `LIVE_TRADING=true`.
- El modo se muestra en dashboard/Telegram.
- El track record es descriptivo y no promete rentabilidad.
- El proyecto incluye disclaimer de alto riesgo y responsabilidad del usuario.

## Estado / evidencia

README y estructura del repositorio confirman implementación. El documento actual de CONTEXT no contiene todavía una auditoría independiente completa del agente; por tanto, los detalles anteriores son documentación del propio repositorio y deben tratarse como VERIFIED DOCUMENTATION, no como certificación independiente.

## Relaciones

- Es un agente de dominio especializado, no parte del Seed genérico.
- Puede beneficiarse del ADN universal ALBRA, pero el dominio financiero debe permanecer aislado.
- No debe absorber responsabilidades de otros especialistas por defecto.
