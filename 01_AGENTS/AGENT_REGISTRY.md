# Registro de agentes ALBRA

- Estado: PROPOSED
- Versión: 0.2.0
- Regla: este registro documenta repositorios y funciones observadas; no concede permisos ni sustituye la documentación de cada agente.

| Agente / sistema | Repositorio | Rol documentado / observado | Estado |
|---|---|---|---|
| Agente General | `ALBRA8/AGENTE-GENERAL` | Seed universal técnico y operativo | FROZEN reference |
| Agente Leads | `ALBRA8/AGENTE-LEADS` | Inteligencia y prospección B2B | VERIFIED DOCUMENTATION |
| Agente Krea | `ALBRA8/AGENTE-KREA` | Product Factory / Commercial Product Engine | CONDITIONALLY APPROVED |
| Agente Chismoso | `ALBRA8/AGENTE-CHISMOSO` | Investigación / inteligencia | PARTIAL — ficha Wave 2 |
| YouTube Automation | `ALBRA8/YOUTUBE-AUTOMATION` | Orquestador/fábrica audiovisual | PARTIAL — ficha Wave 2 |
| Trading | `ALBRA8/AGENTE-TRADING` | Trading institucional SMC + MT5 bridge | VERIFIED DOCUMENTATION |
| Despacho Jurídico | `ALBRA8/DESPACHO-JURIDICO` | Organismo jurídico base | IMPLEMENTED BASE / knowledge not loaded |
| CRM ALBRA | `ALBRA8/CRM-ALBRA` | CRM comercial multi-tenant + IA | VERIFIED DOCUMENTATION |
| NEX-SCOPE | `ALBRA8/NEX-SCOPE` | Discovery/análisis de nichos YouTube | VERIFIED DOCUMENTATION |
| RADAR-SECOP2 | `ALBRA8/RADAR-SECOP2` | Inteligencia de oportunidades SECOP II | VERIFIED DOCUMENTATION |
| Extension-CAZADOR | `ALBRA8/Extension-CAZADOR` | Extensión/herramienta de captura/investigación | PARTIAL |
| PURO-PODER-CONOCIMIENTO | `ALBRA8/PURO-PODER-CONOCIMIENTO` | Sistema de conocimiento | PARTIAL |

## Relaciones relevantes

- Krea crea productos; no debe absorber automáticamente investigación legal/rights clearance.
- Leads y Chismoso investigan, pero la nueva responsabilidad debe clasificarse antes de asignarse.
- YOUTUBE-AUTOMATION es el orquestador audiovisual; HANDS es su capa física.
- NEX-SCOPE aporta discovery de nichos, no ejecución audiovisual.
- RADAR-SECOP2 es un dominio de contratación pública y mantiene sus propios controles de evidencia.
- Despacho Jurídico es el dominio previsto para responsabilidades legales.

## Reglas

1. No inferir capacidades completas desde el nombre de un repositorio.
2. Una ficha especializada debe incluir misión, capacidades, límites, estado, dependencias y evidencia.
3. Si un agente cambia de arquitectura, actualizar su ficha y registrar la decisión correspondiente.
4. No copiar en este registro toda la documentación técnica del agente; enlazar a la fuente.
5. Distinguir VERIFIED, VERIFIED DOCUMENTATION, PARTIAL, INFERRED, ESTIMATED y NOT_VERIFIED.

## Historial

- 0.2.0 — Wave 2 añadió fichas y relaciones adicionales sin eliminar Wave 1.
- 0.1.0 — Registro inicial.
