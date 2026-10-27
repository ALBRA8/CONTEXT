# Registro maestro de proyectos ALBRA — 2026-10-24

Este registro conecta los repositorios de producto con las fichas existentes en CONTEXT. El estado PARCIAL o NOT_VERIFIED indica que existe documentación, pero falta auditoría independiente actual o prueba reproducible.

| Proyecto | Ficha documental | Estado / siguiente paso |
|---|---|---|
| AGENTE-GENERAL | 01_AGENTS/AGENTE-GENERAL.md | Separar Seed histórico de HEAD actual; revisar CI y gates antes de freeze. |
| AGENTE-LEADS | 01_AGENTS/AGENTE-LEADS.md | Conciliar ARNÉS fases 0–7 con fases 8–10 pendientes. |
| AGENTE-CHISMOSO | 01_AGENTS/AGENTE-CHISMOSO.md | No extrapolar el despliegue interno a internet público. |
| AGENTE-KREA | 01_AGENTS/AGENTE-KREA.md | Proveedores y E2E externo siguen sin verificación independiente. |
| AGENTE-TRADING | 01_AGENTS/AGENTE-TRADING.md | Reproducir backtest y bridge MT5 antes de afirmar ejecución real. |
| CRM-ALBRA | 01_AGENTS/CRM-ALBRA.md; informes CRM en 04_PROJECTS | Revisar fix local de WhatsApp sin activar tráfico real. |
| YOUTUBE-AUTOMATION | 01_AGENTS/YOUTUBE-AUTOMATION.md; CONTEXTO YOUTUBE | Verificar HEAD, Flow y GoogleFlowMCP con pruebas autorizadas. |
| NEX-SCOPE | 01_AGENTS/NEX-SCOPE.md | Mantener como plataforma SaaS; integración SRZburner no aprobada. |
| RADAR-SECOP2 | 01_AGENTS/RADAR-SECOP2.md | Reproducir CI y fuentes/MCP en entorno actual. |
| Extension-CAZADOR | 01_AGENTS/EXTENSION-CAZADOR.md | Mantener separada de la extensión Flow de YouTube. |
| PURO-PODER-CONOCIMIENTO | 01_AGENTS/PURO-PODER-CONOCIMIENTO.md | Corpus de referencia; no asumir agente autónomo. |
| DESPACHO-JURIDICO | 01_AGENTS/DESPACHO-JURIDICO.md; 03_AUDITS/DESPACHO_JURIDICO_REPOSITORY_AUDIT_2026-10-26.md | P0: validar CI/workflow y reconstruir la trazabilidad del corpus desde fuentes primarias. Tests, lint, clean-room y E2E no reproducidos; no declarar listo para producción. |
| ERP-ADMON | Fichas generales y auditorías de proyecto | Verificar DIAN, firma, nómina y scripts de base de datos antes de producción. |
| PANEL-DE-CONTROL | 04_PROJECTS/EEC_MISSION_CONTROL.md | Cerrar autenticación, autorización y frontera del proxy antes de exposición. |
| INSTALADOR | 04_PROJECTS/INSTALADOR_EEC_FIRST_DEPLOYMENT_PILOT_2026-10-24.md | E2E autenticado con EEC; Windows y VPS pendientes de prueba. |
| MAXI-APP | Sin ficha específica localizada | Confirmar finalidad, actividad actual y valor comercial. |
| CONTEXT | 00_MASTER_CONTEXT y estructura completa | Mantener índices, decisiones, auditorías y brechas sincronizados. |

## Regla de uso

La fuente de verdad técnica es el repositorio del proyecto. CONTEXT conserva decisiones, evidencias, estado conocido, vínculos y procedencia; no reemplaza el código. Un test o una certificación citada en un commit es evidencia reportada hasta que se reproduzca independientemente.

## Navegación recomendada

1. ALBRA_BOOTSTRAP.md
2. 00_MASTER_CONTEXT/CURRENT_STATE.md
3. 00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md
4. La ficha en 01_AGENTS/ o 04_PROJECTS/
5. El último informe en 03_AUDITS/
6. El repositorio fuente y su HEAD actual
