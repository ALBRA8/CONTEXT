# CRM ALBRA — Hito C2B: respuesta entrante natural y registro en CRM

- **Fecha:** 2026-10-24
- **Estado:** resultado reportado por Antigravity; no revalidado independientemente en esta actualización documental.
- **Proyecto:** CRM ALBRA / Chat Marketing Layer, Capa 2A–2B.
- **Alcance:** registrar el mensaje entrante natural posterior al primer piloto saliente de 2B y preservar las salvaguardas de no respuesta automática.

## 1. Secuencia reportada

1. **10:06:10** — negocio/emisor `573163301286` → cliente/receptor `573017543786`: “Tengo estos zapatos para la venta.” El informe previo registra HTTP 200, Message ID y persistencia outbound en CRM, sin que eso demuestre entrega/lectura.
2. **10:25:14** — cliente `573017543786` → negocio `573163301286`: “Hola”.
3. El informe de Antigravity identifica el mensaje entrante con Baileys `waMessageId=AC3A265C868B7B951FC453B6B7C0A461` y fila CRM `WhatsAppMessage.id=cmvm95zy074hdxh`, `direction=inbound`.
4. Contacto asociado: `Client.id=cmvm4vp02kw9qb6` (Johan Bravo, según el informe). Conversación: `WhatsAppConversation.id=cmvm4vp03soktvf`, visible en CRM `/whatsapp`.
5. Antigravity reportó logs en `task-806.log` y `task-797.log`, asociación correcta y deduplicación por `waMessageId`.

## 2. Salvaguardas durante la observación

- `WhatsAppConversation.isAutoReply=0` aplicado únicamente a la conversación identificada del cliente `573017543786`; el informe indica que el valor original era `1`.
- `NichoConfig.autoReplyEnabled=1` permaneció como configuración global reportada; la pausa por conversación debía evitar la respuesta automática nativa para este hilo.
- `AGENTIC_2A_ENABLED=false` se mantuvo.
- `allowRealDispatch=false` se mantuvo.
- No se autorizó ni reportó un mensaje de respuesta automático al “Hola”; tampoco se autorizó enviar mensajes adicionales para forzar pruebas.
- No restaurar `isAutoReply` a su valor original hasta recibir autorización expresa del propietario.

## 3. Interpretación y límites de evidencia

El hito reportado demuestra la secuencia operativa esperada a nivel de registros: outbound comercial previo, inbound natural posterior, asociación a contacto/conversación y ausencia reportada de respuesta automática. No se inspeccionaron directamente aquí la base de datos local, los logs ni la pantalla del CRM. Por ello, los IDs, timestamps, controles y resultados anteriores se conservan como **evidencia reportada por Antigravity**, no como verificación independiente de esta actualización.

Este hito no certifica entrega/lectura del mensaje saliente, funcionamiento general de producción, capacidad de campañas masivas ni integración completa entre 2A y 2B.

## 4. Estado y siguiente gate

- Mantener la conversación específica con auto-respuesta pausada (`isAutoReply=0`).
- Mantener `AGENTIC_2A_ENABLED=false` y `allowRealDispatch=false`.
- No realizar nuevos envíos ni activar respuestas automáticas sin autorización expresa.
- El siguiente paso es revisión pasiva/documental de las evidencias ya producidas y actualización de contexto; cualquier prueba de respuesta de 2A requiere un plan de control y autorización nuevos.
