# Registro de decisiones ALBRA

- Estado: PROPOSED
- Versión: 0.1.0

Este archivo resume decisiones arquitectónicas que deben poder rastrearse. Una entrada solo se considera aprobada cuando el propietario la haya confirmado explícitamente.

## D-0001 — CONTEXT como memoria externa

- Estado: APPROVED BY REPOSITORY CREATION / pendiente de formalización explícita en este log
- Fecha: 2026-10-12
- Decisión: usar `ALBRA8/CONTEXT` como fuente externa, versionada y portable de continuidad documental de ALBRA.
- Motivo: evitar dependencia exclusiva de una cuenta, conversación o memoria interna de una IA.
- Alcance: contexto documental transversal.

## D-0002 — Escritura exclusiva en CONTEXT durante este trabajo

- Estado: DOCUMENTED IN BOOTSTRAP
- Fecha: 2026-10-12
- Decisión: las IAs que trabajen bajo este contexto pueden modificar únicamente `ALBRA8/CONTEXT`; otros repositorios son de lectura.
- Motivo: proteger agentes y proyectos frente a modificaciones accidentales durante la construcción del contexto.

## D-0003 — Agente General no equivale a orquestador

- Estado: PROPOSED / pendiente de confirmación formal
- Fecha: 2026-10-12
- Decisión conceptual: el Agente General es el organismo base universal, una casa cognitiva/técnica preparada para incorporar capacidades; la orquestación es una responsabilidad separable.
- Motivo: evitar convertir el Seed en un superagente y preservar la especialización.

## D-0004 — ADN separado de capacidades

- Estado: PROPOSED / pendiente de confirmación formal
- Fecha: 2026-10-12
- Decisión conceptual: el ADN contiene identidad, invariantes, seguridad, contratos y gobernanza; las capacidades pueden incorporarse, sustituirse o evolucionar sin alterar silenciosamente el ADN.

## D-0005 — No saturación de especialistas

- Estado: PROPOSED / pendiente de confirmación formal
- Decisión conceptual: una nueva necesidad no se asignará automáticamente a un agente existente. Primero se determinará si es transversal, de dominio, de integración o candidata a un nuevo especialista.

## Plantilla para nuevas decisiones

### D-XXXX — Título

- Estado: PROPOSED / APPROVED / SUPERSEDED / DEPRECATED
- Fecha:
- Decisor / autoridad:
- Decisión:
- Motivo:
- Alternativas consideradas:
- Alcance:
- Impacto:
- Fuentes / evidencia:
- Sustituye a:

## Historial

- 0.1.0 — Registro inicial.
