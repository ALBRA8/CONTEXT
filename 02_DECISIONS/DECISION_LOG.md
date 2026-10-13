# Registro de decisiones ALBRA

- Estado: PROPOSED
- Versión: 0.2.0

Una entrada solo se considera aprobada cuando el propietario la haya confirmado explícitamente. Las decisiones históricas se preservan aunque posteriormente cambien.

## D-0001 — CONTEXT como memoria externa

- Estado: APPROVED BY REPOSITORY CREATION / pendiente de formalización explícita en este log
- Fecha: 2026-10-12
- Decisión: usar `ALBRA8/CONTEXT` como fuente externa, versionada y portable de continuidad documental de ALBRA.

## D-0002 — Escritura exclusiva en CONTEXT durante este trabajo

- Estado: DOCUMENTED IN BOOTSTRAP
- Fecha: 2026-10-12
- Decisión: durante la migración, solo `ALBRA8/CONTEXT` puede modificarse; los demás repositorios son fuentes de consulta.

## D-0003 — Agente General no equivale a orquestador

- Estado: PROPOSED / pendiente de confirmación formal
- Decisión conceptual: Agente General es organismo base universal; la orquestación puede ser responsabilidad separable.

## D-0004 — ADN separado de capacidades

- Estado: PROPOSED / pendiente de confirmación formal
- Decisión conceptual: el ADN contiene identidad, invariantes, seguridad, contratos y gobernanza; las capacidades de dominio se incorporan por especialización.

## D-0005 — No saturación de especialistas

- Estado: PROPOSED / pendiente de confirmación formal
- Decisión conceptual: una nueva necesidad no se asignará automáticamente a un agente existente; primero se clasifica como transversal, dominio, integración o nuevo especialista.

## D-0006 — YOUTUBE-AUTOMATION sigue como orquestador audiovisual

- Estado: EXPLICIT USER DECISION
- Decisión: no crear un Video Editor Agent todavía. Primero se prueban las HANDS; después se decide si hace falta otro agente.

## D-0007 — HANDS antes que Video Editor Agent

- Estado: EXPLICIT USER DECISION
- Decisión: construir/preparar la capa HANDS, probar producción real y decidir después sobre un editor independiente.

## D-0008 — EEC como control plane, no superagente

- Estado: PROPOSED / ACTIVE DESIGN DIRECTION
- Decisión conceptual: EEC/Mission Control debe servir como plano de control visual global y no convertirse automáticamente en un agente superorquestador.

## D-0009 — Pool jurídico especializado

- Estado: EXPLICIT USER DIRECTION / ARCHITECTURE PROPOSAL
- Decisión: si la carga jurídica no encaja en agentes existentes, crear un despacho/pool de abogados con especialistas por área, en vez de saturar Krea, Chismoso o Leads.

## D-0010 — Audiobooks: separar creación de rights research

- Estado: PROPOSED
- Decisión conceptual: Krea puede encargarse de crear el producto/audiolibro, mientras la investigación de dominio público/derechos debe tener una responsabilidad especializada si se vuelve recurrente o sensible.

## D-0011 — Creative Engine vs audiovisual execution

- Estado: EXPLICIT ARCHITECTURAL PRINCIPLE
- Decisión: Creative Engine decide WHAT crear; YOUTUBE-AUTOMATION decide HOW ejecutar la producción.

## D-0012 — Reverse Engineering no copia contenido

- Estado: EXPLICIT FRAMEWORK RULE
- Decisión: Reverse Engineering Engine reconstruye gramática/estructura; los contenidos generados deben ser originales y no copiar personajes, diálogo, secuencia o giro del referente.

## D-0013 — Seed freeze before cloning

- Estado: EXPLICIT WORKFLOW RULE
- Decisión: no clonar AGENTE-GENERAL para crear especializados hasta cerrar la auditoría/freeze del Seed.

## D-0014 — CONTEXT migration is incremental

- Estado: EXPLICIT USER DECISION
- Decisión: las ondas posteriores continúan desde el estado integrado; no recrean ni sobrescriben la migración anterior.

## D-0015 — Autorización de modificación de AGENTE-LEADS para Radar de Clientes

- Estado: EXPLICIT USER DECISION
- Fecha: 2026-10-13
- Decisor / autoridad: Propietario (ALBRA8)
- Decisión: autorizar expresamente la modificación de `ALBRA8/AGENTE-LEADS` para ejecutar el plan de corrección y evolución "Radar de Clientes" (`04_PROJECTS/RADAR_DE_CLIENTES_PLAN.md`), con condiciones: (1) primero P0 de seguridad e integridad; (2) después discovery, inteligencia comercial y experiencia, según prioridades de la auditoría; (3) agentes especializados en paralelo con contratos compartidos; (4) Constructor como único responsable de integración; (5) registro de autorización/hallazgos/plan en CONTEXT sin modificar decisiones anteriores; (6) sin despliegues a producción, envíos comerciales reales, migraciones destructivas ni operaciones de pago no autorizadas; (7) sin credenciales reales en informes/logs; (8) trazabilidad completa de cambios y commits reales; (9) ningún frente declarado terminado sin evidencia verificable.
- Motivo: cerrar P0 confirmados por auditoría (seed-demo, score constante, contacto truncado, errores TS ocultos, SSRF dashboard) y evolucionar el producto.
- Alcance: exclusivamente `ALBRA8/AGENTE-LEADS` (rama dedicada). No sustituye a D-0002 para otros repos. La autorización de modificación NO autoriza despliegue a producción.
- Fuentes/evidencia: mensaje de autorización del propietario 2026-10-13; auditoría `03_AUDITS/AGENTE_LEADS_RADAR_AUDIT_2026-10-13.md`.
- Sustituye a: nada (acota D-0002 para AGENTE-LEADS durante este trabajo).

## Plantilla

### D-XXXX — Título
- Estado: PROPOSED / APPROVED / SUPERSEDED / DEPRECATED
- Fecha:
- Decisor / autoridad:
- Decisión:
- Motivo:
- Alternativas:
- Alcance:
- Impacto:
- Fuentes/evidencia:
- Sustituye a:

## Historial

- 0.2.0 — Wave 2 añadió decisiones históricas explícitas y reglas de evolución sin borrar el log previo.
- 0.1.0 — Registro inicial.
