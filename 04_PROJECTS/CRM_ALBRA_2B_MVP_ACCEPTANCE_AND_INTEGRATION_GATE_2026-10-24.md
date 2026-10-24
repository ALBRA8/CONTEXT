# ALBRA — Cierre de construcción del MVP de Capa 2B y gate de integración

- **Fecha:** 2026-10-24
- **Proyecto:** `clones/albra-broadcast-2b/`
- **Estado declarado:** MVP construido; CP-B01 a CP-B10 reportadas como 10/10 PASS en sala limpia.
- **Estado de integración:** PENDIENTE de validación contractual controlada contra los servicios reales.
- **Estado de campañas reales:** NO AUTORIZADAS.
- **Estado de Capa 2A:** `AGENTIC_2A_ENABLED=false`, reportado como preservado.
- **Fuente:** informe de construcción y aceptación aportado por el propietario en la sesión del 2026-10-24.

## 1. Dictamen

Se registra el MVP de Capa 2B como **construido y con suite de aceptación reportada aprobada**. Esto permite cerrar la fase de especificación/construcción inicial y avanzar a la integración controlada; no equivale a certificar producción ni a autorizar campañas reales.

No se repitieron ni se afirman aquí pruebas independientes. Los resultados de la tabla siguiente provienen del informe del implementador/propietario.

## 2. Pruebas de aceptación reportadas

| ID | Caso | Resultado reportado |
|---|---|---|
| CP-B01 | Ingesta, canonización estricta CSV y deduplicación | PASS |
| CP-B02 | Prefiltro de locks activos de 2A | PASS |
| CP-B03 | Cancelación Just-In-Time por lock activo | PASS |
| CP-B04 | Fail-closed ante caída/timeout de 2A | PASS |
| CP-B05 | Exclusión por STOP/BAJA | PASS |
| CP-B06 | Spintax determinista por semilla | PASS |
| CP-B07 | Intervalo y jitter configurable 15–35 s | PASS |
| CP-B08 | Pausa de enfriamiento cada 25 mensajes | PASS |
| CP-B09 | Horario comercial y estado `PAUSED_OFF_HOURS` | PASS |
| CP-B10 | Recuperación tras caída sin dobles envíos, según prueba | PASS |

El informe indica que la suite utilizó `MockDispatcher` y no realizó envíos reales a WhatsApp.

## 3. Arquitectura reportada

- Paquete aislado en `clones/albra-broadcast-2b/`, con servidor HTTP satélite previsto para puerto 3004.
- Base SQLite operativa propia: `db/broadcast.db`; sin acceso directo a la base comercial del CRM.
- Sin socket WhatsApp/Baileys propio; despacho delegado al daemon/CRM por `POST /send` en puerto 3002 con `INTERNAL_API_SECRET`.
- Consulta de locks de 2A antes de encolar y justo antes del despacho; fallos, timeout o estados desconocidos deben bloquear el envío.
- Supresiones STOP/BAJA y límite de frecuencia de un broadcast por contacto cada siete días.
- Clave de idempotencia declarada: `hash(campaign_id + phone)`; la semántica de entrega exactamente una vez no debe afirmarse sin soporte contractual del daemon y reconciliación de resultados inciertos.

## 4. Frontera pendiente antes de integrar

No se requiere otra auditoría general ni repetir toda la suite. La siguiente fase debe limitarse a comprobar la integración real, sin despachar mensajes:

1. **Contrato del daemon CRM:** ruta, autenticación, esquema de petición/respuesta, normalización del teléfono, códigos de error y comportamiento ante timeout.
2. **Resultado de despacho incierto:** distinguir fallo confirmado de envío potencialmente aceptado antes de una caída; no reintentar automáticamente un caso ambiguo si podría duplicar el mensaje.
3. **Locks y supresiones 2A:** confirmar las rutas y esquemas reales, estados reconocidos, tiempos límite y autoridad de la lista de bajas.
4. **Configuración segura:** secretos por entorno, no incluidos en logs, código, fixtures ni documentación; `AGENTIC_2A_ENABLED=false` se conserva.
5. **Modo de integración sin envío:** usar simulación, adaptador de no-op o stub contractual para verificar las llamadas y estados; prohibido usar destinatarios reales para este gate.

## 5. Límites y decisiones de seguridad

- Este documento no confirma que el repositorio del producto haya sido inspeccionado independientemente ni que los tests se hayan ejecutado en esta operación.
- No se ha certificado compatibilidad E2E real entre 2B, 2A y CRM/daemon.
- No se autoriza campaña real, envío manual de prueba, activación de tráfico de producción ni cambio de `AGENTIC_2A_ENABLED`.
- La recuperación de proceso y la clave única son garantías dentro del comportamiento probado reportado; no demuestran por sí solas entrega exactamente una vez en el sistema distribuido.
- La implementación debe respetar consentimiento, bajas, políticas del canal y frecuencia legítima. No se diseñan técnicas de evasión de filtros antispam ni se promete inmunidad a bloqueos.

## 6. Siguiente hito

**Integración contractual controlada, sin mensajes reales.** Al terminar, presentar únicamente: contratos confirmados, discrepancias encontradas y corregidas, resultado de la prueba integrada sin envío, y riesgos residuales. La autorización de campañas reales se decide en una fase posterior explícita.

## 7. Evidencia de origen

Informe del propietario aportado el 2026-10-24: construcción de los módulos `types.ts`, `db.ts`, `csv-importer.ts`, `spintax.ts`, `lock-checker.ts`, `suppression.ts`, `throttler.ts`, `broadcast-worker.ts`, `dispatcher.ts`, `server.ts` y `tests/suite.test.ts`; resultados CP-B01–CP-B10 declarados 10/10 PASS.
