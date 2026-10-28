# ALBRA — Reconocimiento general del ecosistema (2026-10-27)

## Propósito y alcance
Registro de una primera pasada de reconocimiento de los repositorios visibles de `ALBRA8`, para recuperar contexto general y orientar una auditoría integrada posterior. Esta revisión es documental/estática y focalizada: no equivale a una auditoría completa de cada repositorio, ejecución de pruebas, pentest ni verificación de despliegues.

## Fuente canónica
`ALBRA8/CONTEXT` continúa siendo la memoria institucional y el único repositorio que debe recibir cambios de contexto en este flujo. Los repositorios de producto se consultan como fuentes, no se modifican.

## Mapa inicial de sistemas
- **Seed y arquitectura transversal:** `AGENTE-GENERAL`. El README lo describe como base universal técnica, cognitiva, de memoria, seguridad, ejecución, observabilidad e integración; no como agente especializado. CONTEXT distingue el seed congelado histórico del HEAD posterior, que quedó NOT_READY_TO_FREEZE pendiente de CI/auditoría.
- **Comercial:** `CRM-ALBRA` expone contratos REST/MCP para hosts externos y gestiona clientes, oportunidades, canales y automatizaciones; `AGENTE-LEADS` realiza prospección e inteligencia B2B. Los contratos entre sistemas deben comprobarse en ambos extremos antes de declarar integración E2E.
- **Mensajería:** `WHATSPOWER` se presenta como Nivel 2 para flujos conversacionales; `WHATSPOWER-ULTRA` como Nivel 3 para campañas/difusión. La existencia de adaptadores o módulos no demuestra entrega externa real.
- **Gestión empresarial:** `ERP-ADMON` se presenta como ERP administrativo/contable complementario al CRM; `CONTADOR-PUBLICO` se enfoca en apoyar la preparación de renta de personas naturales en Colombia (Formulario 210), con motor determinista y revisión humana.
- **Comunidad y contenidos:** `COMUNITI-MANAGER` se describe como arnés de community management con varios agentes, memoria de marca, aprobaciones humanas y conexiones con otros sistemas ALBRA. `AGENTE-KREA` apoya la creación de productos/assets.
- **Jurídico:** `DESPACHO-JURIDICO` tiene una base técnica documentada, pero su README indica que todavía no incluye conocimiento jurídico cargado ni subagentes especializados.
- **Investigación y oportunidades:** `AGENTE-CHISMOSO`, `NEX-SCOPE` y `RADAR-SECOP2` tienen ámbitos diferenciados de investigación, descubrimiento de nichos y oportunidades públicas.
- **Producción y conocimiento:** `YOUTUBE-AUTOMATION` es el sistema de automatización audiovisual; `PURO-PODER-CONOCIMIENTO` es un corpus/sistema de conocimiento, no necesariamente un agente autónomo.
- **Control y operación:** `PANEL-DE-CONTROL` (EEC) se presenta como plano de control con estado único, validación, autorización, políticas, simulación explícita y aprobaciones humanas. `INSTALADOR` aparece en el inventario, pero no se encontró un README en la ruta probada.
- **Otros repositorios visibles:** `AGENTE-TRADING`, `Extension-CAZADOR`, `MAXI-APP` y `CHAT-X`; requieren revisión específica para documentar su estado, propósito y relaciones.

## Reglas de lectura que se mantienen
1. Separar documentación declarada, evidencia observada, pruebas ejecutadas y operación en producción.
2. Un commit o un README no certifican por sí solos la seguridad, la integración ni la disponibilidad.
3. No mezclar estado histórico con HEAD actual.
4. No ejecutar acciones externas sensibles —mensajes, campañas, pagos, operaciones de trading o cambios productivos— como parte de una revisión de contexto.
5. Evitar duplicar responsabilidades: definir contratos entre Seed, agentes especializados, CRM, ERP y arneses de operación.
6. Para contabilidad y derecho, preservar trazabilidad de fuentes y revisión humana para decisiones o presentaciones de alto impacto.

## Estado y limitaciones de esta pasada
Se consultaron el bootstrap, estado, índice, registro de agentes y matriz de auditorías de CONTEXT, y se leyeron README de una selección de repositorios clave. La matriz canónica tiene evidencia histórica que no necesariamente coincide con todos los HEAD visibles actuales. Algunos README solicitados por ruta devolvieron 404; eso significa que esa ruta concreta no se pudo leer, no que el repositorio esté vacío.

No se ejecutaron pruebas, builds, análisis dinámicos ni verificaciones de despliegue. No se modificaron repositorios de producto. La lista del inventario visible de GitHub debe tratarse como inventario inicial, no como garantía de que incluya todo lo que existe fuera de la cuenta o instalación conectada.

## Próxima secuencia recomendada
1. Enumerar todos los repositorios visibles y registrar HEAD/fecha por repositorio.
2. Leer bootstrap/README/manifiestos de pruebas y documentación de arquitectura de cada uno.
3. Clasificar cada proyecto como idea, diseño, implementación, pruebas reportadas, pruebas reproducidas o producción verificada.
4. Construir un mapa de dependencias y contratos con dirección, autenticación, esquema de eventos, idempotencia, errores y responsables.
5. Priorizar riesgos compartidos (secretos, autorización, aislamiento multi-tenant, webhooks, operaciones irreversibles).
6. Actualizar las fichas de agentes y la matriz canónica con evidencia fechada, manteniendo las incertidumbres explícitas.
