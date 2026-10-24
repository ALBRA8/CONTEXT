# CRM ALBRA + Chat Marketing Layer — Arquitectura por capas

- **Fecha:** 2026-10-24
- **Estado arquitectónico:** decisión conceptual acordada; diseño de contratos pendiente.
- **Estado de implementación:** NO INICIADA / NO AUTORIZADA en esta fase.
- **Repositorio base:** [ALBRA8/CRM-ALBRA](https://github.com/ALBRA8/CRM-ALBRA)
- **Fuente de continuidad:** conversación del 2026-10-24 y matriz de investigación local reportada por el propietario.
- **Alcance de esta actualización:** documentación institucional en ALBRA8/CONTEXT; no se modificó CRM-ALBRA ni los clones locales.

## 1. Decisión central

La ampliación comercial de CRM ALBRA se organizará en **dos capas funcionales nuevas** que se apoyan en la **Capa 1 existente**. No se crearán tres productos técnicos independientes ni un CRM paralelo.

1. **Capa 1 — CRM ALBRA / Chaleco:** sistema comercial canónico existente.
2. **Capa 2A — CRM Agéntico / Conversacional:** motor para gestionar conversaciones, flujos, decisiones y acciones comerciales.
3. **Capa 2B — Superpowers / Chat Marketing:** motor para activar audiencias y gestionar campañas a escala.

2A y 2B son dos dominios separados dentro de la iniciativa de ampliación del CRM. Deben poder evolucionar independientemente y comunicarse mediante contratos explícitos.

**No crear NEXFLOW como sistema independiente.** Los nombres WhatsPower/NEXFLOW son provisionales y no determinan por sí solos la arquitectura definitiva. La ficha actual `clones/whatspower/FICHA.md` se considera material de investigación, no una autorización automática de implementación.

## 2. Responsabilidades

### Capa 1 — CRM ALBRA / Chaleco

Es la fuente canónica de los datos y el estado comercial:

- Contactos, clientes y empresas.
- Oportunidades y pipeline.
- Cotizaciones, calendario y productos/servicios.
- Timeline e historial comercial.
- Usuarios, permisos, aislamiento multi-tenant y auditoría.
- Canales y daemon de WhatsApp ya existentes.
- Automatizaciones de negocio que ya posee.
- APIs, MCP y contratos de integración existentes.

Las capacidades reales y operativas deben verificarse en el repositorio actual antes de diseñar contratos. La documentación histórica o los reportes de pruebas no sustituyen una auditoría actual.

### Capa 2A — CRM Agéntico / Conversacional

**Pregunta que resuelve:** “¿Qué decir, qué preguntar y qué acción comercial tomar con este contacto?”

Capacidades candidatas a investigar:

- Constructor visual de flujos por nodos y ramificaciones.
- Triggers por palabras clave, mensajes, respuestas y eventos.
- Secuencias y respuestas automáticas.
- Agentes conversacionales/Salesbot.
- Calificación de leads y preguntas estructuradas.
- Captura de campos personalizados mediante contratos de CRM.
- Respuestas rápidas y opciones interactivas.
- Contexto y estado de cada conversación.
- Memoria conversacional separada de la memoria comercial canónica.
- Handoff a humano y resumen para el vendedor.
- Acciones autorizadas sobre oportunidades, etiquetas y etapas del pipeline.
- Follow-ups y recuperación de leads.
- Campañas conversacionales controladas.
- IA que propone o ejecuta acciones dentro de permisos explícitos.

La capa 2A no se limita a conversaciones inbound: puede recibir conversaciones originadas orgánicamente, por campañas, follow-ups u otros canales. Debe existir una política clara de propiedad de conversación para impedir respuestas duplicadas entre automatizaciones.

Referencias funcionales iniciales: Kommo, ManyChat, Zernflow, ChatbotX y OpenReply. Sus capacidades deben analizarse, no copiarse literalmente.

### Capa 2B — Superpowers / Chat Marketing

**Pregunta que resuelve:** “¿A quién activar, cuándo y con qué controles para escalar una operación de marketing?”

Capacidades candidatas a investigar:

- Campañas y broadcast.
- Importación de CSV/Excel y selección de segmentos del CRM.
- Segmentación, supresiones y exclusiones.
- Personalización y Spintax.
- Planificación, programación y control de frecuencia.
- Rate limiting, delays y enfriamiento por lotes.
- Pausar, reanudar y cancelar campañas.
- Estado de despacho, fallos, métricas y reportes.
- Enlaces rastreables y atribución.
- Pruebas A/B y optimización.
- Analítica avanzada e inteligencia de audiencia.
- Recuperación de leads a escala.
- Orquestación multicanal cuando existan conectores y permisos adecuados.
- Protección de reputación, consentimiento, bajas y controles de abuso.

La investigación de WhatsOn es el punto de partida para broadcast y maquinaria de envío. La etiqueta “anti-ban” de la ficha inicial no se considera garantía técnica ni autorización para evadir controles de plataforma. El diseño debe centrarse en uso legítimo, políticas del canal, consentimiento cuando corresponda, límites de frecuencia, bajas, supresión y prevención de abuso. No se puede prometer ausencia de bloqueos ni entregabilidad garantizada.

## 3. Puente entre 2A y 2B

Las capas no deben depender de las implementaciones internas de la otra. El intercambio debe producirse por contratos/eventos con identificadores de correlación y semántica definida.

Flujo de referencia:

1. 2B ejecuta una campaña autorizada y registra sus resultados operativos.
2. El canal/CRM recibe una respuesta y mantiene el historial canónico según los contratos existentes.
3. Un evento de respuesta se enruta sin duplicación al motor conversacional.
4. 2A decide si activar un flujo, calificar al lead, responder o derivar a un humano.
5. Las acciones comerciales se ejecutan mediante las APIs/MCP autorizados de CRM ALBRA.
6. CRM conserva el estado canónico del contacto y de la oportunidad; 2A/2B conservan solo el estado operativo propio de sus dominios.

Deben diseñarse idempotencia, correlación de eventos, reintentos, gestión de errores y prevención de procesamiento/respuesta duplicados.

## 4. Contratos a diseñar — todavía no implementados

Primero auditar qué contratos existen realmente. Después proponer los mínimos necesarios:

- **CRM → 2A:** notificaciones de mensajes entrantes, cambios relevantes de conversación y eventos comerciales.
- **2A → CRM:** solicitudes autorizadas para enviar/responder, actualizar campos, añadir notas, cambiar oportunidades/etapas y realizar handoff.
- **CRM → 2B:** acceso autorizado a audiencias/segmentos y datos mínimos necesarios para campañas.
- **2B → CRM:** estados de campaña/despacho, resultados relevantes, respuestas recibidas y bajas/supresiones cuando aplique.
- **2B → 2A (preferiblemente mediante evento o router acordado):** señal de que un contacto respondió y referencia a la campaña/conversación.

REST, MCP, webhooks o eventos son opciones a evaluar, no requisitos acumulativos. No inventar nombres de endpoints ni afirmar que eventos sugeridos ya existen. Todo acceso debe respetar tenant, autenticación, RBAC, minimización de datos y auditoría.

## 5. Matriz de clasificación obligatoria

Cada capacidad descubierta en una referencia debe etiquetarse como:

1. Ya existe en CRM ALBRA.
2. Pertenece a 2A.
3. Pertenece a 2B.
4. Es compartida y necesita un contrato.
5. Está duplicada o no justifica su coste.
6. Pendiente de investigación/decisión.

La matriz reportada por el propietario en `investigacion_camino/MATRIZ_ARQUITECTURA_CAPAS_ALBRA.md` y la ficha `clones/whatspower/FICHA.md` son fuentes de trabajo que deben cotejarse con la auditoría del repositorio. No se han verificado desde este cambio documental.

## 6. Secuencia de trabajo y gates

**Fase 1 — Auditoría:** inspeccionar el estado actual de CRM ALBRA y las investigaciones/clones disponibles; registrar ramas/HEAD cuando sea posible y distinguir código real de documentación.

**Fase 2 — Clasificación:** completar la matriz de capacidades, duplicaciones, dependencias y riesgos.

**Fase 3 — Diseño:** definir los límites 2A/2B, propiedad de datos, contratos mínimos, seguridad, eventos e idempotencia.

**Fase 4 — Revisión:** presentar arquitectura, decisiones pendientes, licencias y riesgos.

**Fase 5 — Aprobación explícita:** no iniciar construcción funcional, conectar canales ni realizar envíos reales hasta recibir autorización.

Durante las fases actuales no modificar la lógica de producción de CRM ALBRA, no desplegar, no conectar componentes experimentales a canales de producción y no declarar pruebas/integraciones verificadas sin evidencia.

## 7. Referencias y límites de evidencia

- CRM ALBRA: https://github.com/ALBRA8/CRM-ALBRA
- Investigación Kommo indicada en la conversación: `clones/whatspower/investigacion/informe.md`.
- Ficha de trabajo: `clones/whatspower/FICHA.md`.
- Matriz reportada: `investigacion_camino/MATRIZ_ARQUITECTURA_CAPAS_ALBRA.md`.
- Referencias de investigación mencionadas: WhatsOn, Kommo, ManyChat, Zernflow, ChatbotX y OpenReply.

Esta entrada registra la decisión conceptual y las instrucciones de trabajo. **No certifica que los repositorios/clones locales, los contratos ni las capacidades se hayan inspeccionado o probado en esta operación.** El siguiente paso es que Antigravity continúe la investigación con esta separación, entregue auditoría/matriz/contratos propuestos y espere aprobación antes de construir.
