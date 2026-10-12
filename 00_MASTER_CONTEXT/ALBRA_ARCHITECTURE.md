# Arquitectura maestra de ALBRA

- Estado: PROPOSED
- Versión: 0.1.0
- Propósito: describir la relación entre ADN, Agente General, agentes especializados, capacidades y contexto.

## 1. Principio central

ALBRA no es un único agente que lo hace todo. Es un ecosistema de agentes especializados que comparten un ADN técnico, operativo, cognitivo y de seguridad común.

El principio de diseño es especialización sin contaminación: una capacidad debe vivir en el componente que corresponde a su naturaleza y no añadirse a otro agente solo porque esté disponible.

## 2. Agente General

El Agente General es el organismo base universal de ALBRA. No debe definirse principalmente como jefe, director u orquestador.

### Analogía de la casa

El Agente General es una casa vacía pero completamente equipada: tiene estructura, electricidad, agua, internet, seguridad, conectores, espacios y servicios fundamentales. Cuando llega un nuevo ocupante, puede incorporar aquello que necesite —conocimiento, herramientas, software, MCP, APIs, procedimientos o capacidades— sin tener que reconstruir la casa.

Por tanto:

- **ADN:** lo que la casa es y las reglas que no puede perder.
- **Capacidades:** lo que la casa puede incorporar.
- **Especialización:** en qué tipo de habitante/proyecto se convierte.
- **Orquestación:** una responsabilidad independiente que puede existir cuando un sistema concreto la necesite.

## 3. Relación con AGENTE-GENERAL Seed

El repositorio `ALBRA8/AGENTE-GENERAL` contiene el Seed técnico universal: runtime, identidad, contexto, memoria, evidencia, seguridad, ejecución, observabilidad, MCP, handoff, extensibilidad y demás infraestructura transversal. Su README establece explícitamente que el Seed no contiene conocimiento de dominio y que las especializaciones se añaden fuera de `src/albra_seed/`.

El Seed congelado referenciado actualmente es `8437f2a`; su certificación documental está en `00a353c`. Estas referencias son de solo lectura desde `CONTEXT`.

## 4. Agentes especializados

Un agente especializado conserva el ADN común y añade solamente lo que requiere su misión. Ejemplos documentados o en construcción incluyen Leads, Krea, Chismoso, Trading, YouTube Automation y Despacho Jurídico.

No asumir que todos los agentes tienen las mismas funciones ni convertir una capacidad especializada en una obligación transversal.

## 5. Capacidades

Una capacidad puede ser una herramienta, MCP, API, proveedor, software, base de datos, servicio, automatización, agente auxiliar, flujo o infraestructura externa.

Modelo conceptual:

`ALBRA CORE → CAPABILITY CONTRACT → CAPABILITY REGISTRY → ADAPTER / CONNECTOR → EXTERNAL CAPABILITY`

La capacidad debe tener estado explícito y evidencia cuando se afirme que está implementada o verificada.

## 6. Contexto

`ALBRA8/CONTEXT` documenta la continuidad transversal. No reemplaza a los repositorios de implementación.

La separación recomendada es:

- CONTEXT → qué sabemos, qué decidimos, qué está vigente y dónde está la fuente.
- AGENTE-GENERAL → infraestructura universal.
- Agentes especializados → conocimiento, herramientas y workflows de dominio.
- Proyectos → aplicaciones o iniciativas concretas.
- Auditorías → evidencia y estado de verificación.

## 7. Regla de no saturación

Si una nueva necesidad pertenece naturalmente a otro dominio, no se añade automáticamente a un agente existente. Primero se evalúa si es capacidad transversal, capacidad del dominio actual, integración externa o candidato a nuevo agente especializado.

## Historial

- 0.1.0 — Primera arquitectura maestra documentada a partir del Seed y del diseño ALBRA vigente.
