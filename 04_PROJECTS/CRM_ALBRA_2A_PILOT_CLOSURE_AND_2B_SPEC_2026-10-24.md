# ALBRA — Cierre del Hito 2A y especificación de Capa 2B

- **Fecha de registro:** 2026-10-24
- **Estado de Capa 2A:** hito piloto real supervisado declarado superado según el informe aportado por el propietario.
- **Estado de Capa 2B:** especificación técnica autorizada; implementación aún no autorizada.
- **Fuente:** informe de cierre y propuesta presentados en la conversación del 2026-10-24.
- **Nivel de evidencia:** VERIFIED DOCUMENTATION / reporte del propietario. Los registros y la ejecución local no se inspeccionaron independientemente durante esta actualización.
- **Alcance de escritura:** exclusivamente documentación en `ALBRA8/CONTEXT`; no se modificaron CRM-ALBRA, los clones locales ni código de producto.

## 1. Cierre de Capa 2A

Según el informe aportado, el piloto supervisado recorrió esta arquitectura:

`WhatsApp móvil → daemon Baileys (:3002) → CRM ALBRA (:3000) → HTTP firmado con HMAC → Capa 2A (:3003)`

Resultado reportado para el mensaje `PRECIO`:
- La regla determinística `keyword_rule` generó la respuesta sin invocar el LLM del CRM.
- Latencia reportada: **321 ms**, frente a un timeout configurado/reportado de 3500 ms.
- Capa 2A devolvió `replyText` por HTTP; el CRM/daemon Baileys conservó la autoridad exclusiva de despacho físico a WhatsApp.
- El informe indica una sola respuesta, sin duplicados.
- Allowlist del piloto limitada al contacto `3017543786`, canonizado como `573017543786`; número emisor reportado: `3163301286`.
- Después de la prueba se restauró `AGENTIC_2A_ENABLED=false`, y el informe indica que Next.js recargó `.env`.

### Estado operativo y seguridad

- `AGENTIC_2A_ENABLED=false`: estado reportado como confirmado y en reposo.
- No se autoriza más tráfico real ni ampliar la allowlist sin una nueva autorización expresa.
- Se conserva como observación la marca `crmSynced: false`. Según la explicación aportada, las reglas determinísticas de catálogo/precios no modifican entidades comerciales y `crmSynced: true` se reserva para derivaciones humanas o creación de prospectos. Esta interpretación queda documentada como explicación reportada, no como verificación independiente del código.
- El CRM sigue siendo la fuente comercial canónica y la única autoridad de despacho. Capa 2A no debe abrir sockets de WhatsApp propios ni acceder directamente a la base comercial.

## 2. Decisión para Capa 2B

Se autoriza **avanzar a la especificación técnica y preparar `FICHA.md`** para el proyecto previsto en `clones/albra-broadcast-2b/`. Esta autorización no incluye escribir código, conectar servicios ni enviar campañas.

Responsabilidad propuesta: campañas salientes controladas, gestión de audiencias, colas y lotes, personalización legítima, control de frecuencia, supresiones, estados de despacho y métricas. Capa 2B no atiende mensajes entrantes ni sustituye el motor conversacional 2A.

### Límites arquitectónicos obligatorios

1. **Cero sockets de WhatsApp en 2B.** El despacho se realiza mediante una interfaz contractual existente y autorizada del CRM; el endpoint real debe verificarse antes de especificar la integración. No asumir que `POST /api/inbox/send` o `POST /api/crm/actions` existen o son adecuados sin inspección.
2. **Sin acceso directo a la base comercial.** La base local propuesta `clones/albra-broadcast-2b/db/broadcast.db` solo conservaría estado operativo propio de campañas.
3. **Sin diálogo inbound en 2B.** Las respuestas a campañas ingresan por el circuito normal CRM → Capa 2A o asesor humano, según los contratos de enrutamiento vigentes.
4. **Locks conversacionales.** Excluir contactos en `IN_FLOW_2A` o `HANDOFF_HUMAN`. Comprobar al seleccionar/encolar y volver a comprobar justo antes del despacho. Si la consulta falla, vence por timeout o devuelve estado desconocido, no enviar. La ficha debe describir cómo reducir la ventana de carrera entre la comprobación final y el despacho.
5. **Privacidad y control de acceso.** Autenticación server-to-server, secretos fuera de CONTEXT, minimización de datos, aislamiento por tenant, auditoría, idempotencia y protección de listas de supresión.
6. **Consentimiento y bajas.** Registrar procedencia y base de autorización de las audiencias según corresponda; respetar bajas, supresiones y políticas aplicables del canal.
7. **Variación de contenido.** Spintax permitido para variaciones naturales y legítimas, no como mecanismo de evasión de sistemas antispam. Los controles de frecuencia no garantizan ausencia de bloqueos.

### Parámetros operativos propuestos, aún configurables

- Intervalo entre contactos: 15–35 segundos.
- Enfriamiento: 5–8 minutos cada bloque de 25 mensajes.
- Máximo propuesto: un mensaje masivo por contacto cada siete días.
- Horarios comerciales hábiles.
- Supresión automática para contactos dados de baja.

Estos valores se registran como parámetros iniciales propuestos sujetos a validación operativa, consentimiento y políticas aplicables; no son garantías de entregabilidad ni de prevención de bloqueos.

## 3. Dependencias que debe resolver la FICHA.md

- Verificar la interfaz real del CRM para consultar audiencias y despachar mensajes, incluyendo autenticación, permisos, auditoría, errores e idempotencia.
- Verificar la interfaz real de Capa 2A para consultar locks: ruta, autenticación, estados, semántica, timeout y comportamiento ante indisponibilidad.
- Definir modelo de datos propio de `broadcast.db`, estados de campaña/cola/contacto, reintentos, recuperación tras reinicio y prevención de duplicados.
- Definir contratos de eventos/correlación para relacionar campaña, mensaje enviado, respuesta entrante y conversación.
- Documentar criterios de aceptación y decisiones pendientes sin fingir que endpoints o contratos propuestos ya existen.

## 4. Fases y autorización

**Autorizado ahora:** documentación técnica, contratos propuestos, dependencias y criterios de aceptación.

**No autorizado todavía:** implementación de código de producción, creación/modificación de bases de datos operativas, conexión de servicios, activación de campañas, mensajes reales, modificación de la configuración de 2A o ampliación de allowlists.

Al finalizar `FICHA.md`, el constructor debe detenerse y presentar decisiones cerradas, dependencias pendientes y criterios de aceptación. La implementación requerirá autorización expresa posterior.

## 5. Procedencia y límites

Este documento conserva el resultado reportado del piloto y la decisión arquitectónica del propietario. No afirma que esta operación haya reproducido la prueba, inspeccionado logs locales o verificado los repositorios de producto. El registro en CONTEXT no equivale a una certificación técnica independiente ni a autorización de producción.
