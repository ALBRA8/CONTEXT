# ALBRA DNA — Baseline propuesta

- Estado: PROPOSED
- Versión: 0.1.0
- Alcance: principios e invariantes arquitectónicos iniciales de ALBRA.
- Autoridad: esta línea base no se considera aprobada hasta revisión explícita del propietario.

## 1. Identidad

ALBRA es una arquitectura pensada para mantener continuidad, identidad operativa, decisiones y capacidades sin depender exclusivamente de una cuenta, un modelo de IA o una conversación concreta.

ALBRA-CONTEXT es la memoria externa versionada que documenta ese contexto. El repositorio no equivale por sí solo a la identidad completa de ALBRA.

## 2. ADN y capacidades

El ADN define identidad, principios, límites de seguridad, contratos fundamentales, trazabilidad, gobernanza y reglas de evolución.

Las capacidades son adquiribles o sustituibles: herramientas, conectores, MCP, APIs, software, bases de datos, agentes especializados, automatizaciones y otros servicios. Añadir o reemplazar una capacidad no debe alterar silenciosamente el ADN.

Arquitectura conceptual de capacidades:

`ALBRA CORE → CAPABILITY CONTRACT → CAPABILITY REGISTRY → ADAPTER / CONNECTOR → EXTERNAL CAPABILITY`

Esta secuencia es un modelo conceptual de referencia, no una afirmación de que todos sus componentes estén ya implementados.

## 3. Agente General

El Agente General es el organismo base universal de ALBRA. La analogía acordada es **una casa vacía preparada para recibir a cualquier persona**: dispone de estructura y servicios fundamentales, sin estar definida por la misión particular de un ocupante.

No debe reducirse por defecto a un coordinador, orquestador o jefe de otros agentes. Los agentes especializados heredan el ADN común y añaden una misión, contratos y capacidades específicas.

## 4. Invariantes iniciales

1. Mantener separadas la identidad fundamental y las capacidades intercambiables.
2. Hacer explícitos los cambios de ADN y las decisiones que los autorizan.
3. Distinguir propuestas, aprobaciones, declaraciones y verificaciones.
4. Conservar trazabilidad hacia documentos, commits, pruebas y decisiones.
5. Aplicar seguridad por defecto y no publicar secretos.
6. No alterar el Seed congelado de `ALBRA8/AGENTE-GENERAL` sin una autorización explícita y una decisión formal posterior.
7. No afirmar que una capacidad existe o fue validada sin evidencia adecuada.
8. Diseñar para portabilidad entre modelos, cuentas, conversaciones y plataformas, sin suponer sincronización automática.

## 5. Gobernanza

Una IA puede analizar, proponer, redactar y verificar dentro de sus capacidades y permisos. No debe atribuir al propietario una aprobación que no ocurrió ni convertir una propuesta en norma por el mero hecho de escribirla.

Si dos fuentes autorizadas se contradicen, registrar el conflicto, evaluar su procedencia y solicitar una decisión cuando no exista resolución inequívoca.

## 6. Estado y límites

Esta línea base reúne principios acordados en el proceso inicial de diseño y los organiza en un documento. Sigue en estado `PROPOSED`: debe compararse con fuentes canónicas previas de ALBRA antes de aprobarse como ADN definitivo.

## Historial

- 0.1.0 — Línea base propuesta a partir del contexto de diseño disponible. Pendiente de revisión y aprobación.
