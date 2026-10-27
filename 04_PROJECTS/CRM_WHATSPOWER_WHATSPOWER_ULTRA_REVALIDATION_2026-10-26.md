# Revalidación de repositorios — CRM-ALBRA, WHATSPOWER y WHATSPOWER-ULTRA

- **Fecha:** 2026-10-26
- **Objetivo:** volver a revisar los tres enlaces fuente aportados y confirmar el estado del registro canónico previo en CONTEXT.
- **Fuentes canónicas leídas antes de actuar:** `ALBRA_BOOTSTRAP.md`, `00_MASTER_CONTEXT/CURRENT_STATE.md`, `00_MASTER_CONTEXT/KNOWLEDGE_INDEX.md` y el informe cruzado del 2026-10-26.
- **Alcance:** metadatos de repositorios, HEAD público, commits recientes, README, árbol recursivo y lectura estática de código seleccionado. No se ejecutaron builds, suites, servidores ni E2E.
- **Escrituras:** solo este repositorio CONTEXT. No se modificaron CRM-ALBRA, WHATSPOWER ni WHATSPOWER-ULTRA.

## Estado de los HEAD públicos

| Repositorio | Rama | HEAD confirmado en esta pasada | Fecha del HEAD |
|---|---|---|---|
| [CRM-ALBRA](https://github.com/ALBRA8/CRM-ALBRA) | main | `db95a5bec99bb85298f7ecbd6c630ebb8b60e669` | 2026-10-22 |
| [WHATSPOWER](https://github.com/ALBRA8/WHATSPOWER) | main | `d8547491630a3023013a2dd49dca31c7e18f9a9d` | 2026-10-26 |
| [WHATSPOWER-ULTRA](https://github.com/ALBRA8/WHATSPOWER-ULTRA) | main | `b7b5a5dabe7e84ecd3c74171dfaa0c9b75b340c9` | 2026-10-26 |

Los hashes coinciden con el informe cruzado anterior. La API de GitHub devolvió estos commits como los más recientes en cada `main`; en CRM el commit reporta 381/381 tests, TypeScript 0 errores y ESLint limpio, pero esas pruebas no fueron rerun en esta revisión.

## Hallazgos revalidados

### CRM-ALBRA

- `src/whatsapp-daemon/daemon.mjs` existe en `main`; el código leído exige `INTERNAL_API_SECRET` de mínimo 24 caracteres al iniciar el daemon y conserva asociación explícita de la sesión a una organización.
- `src/whatsapp-daemon/dispatch-gate.mjs` devuelve 404 en el Contents API de la rama predeterminada y no aparece acreditado como incorporado al HEAD público. Los resultados D.5/E.2.R reportados en un entorno local no deben atribuirse a `main` sin commit/branch verificable.
- Precaución conocida: `package.json` tiene un comando de sincronización Prisma con `--accept-data-loss`; no ejecutarlo sin inspección del esquema/entorno y backup.

### WHATSPOWER — Nivel 2

- El HEAD incluye módulos de equipo/round-robin, respuestas rápidas, growth links, triggers de etapa CRM y enlaces de pago.
- `src/server.ts` crea `MockTransportSender` y `MockCrmClient` por defecto; la conexión real requiere configuración explícita.
- La firma inbound HMAC se comprueba cuando se entrega firma o cuando `NODE_ENV === 'production'`. Por tanto, en configuraciones no productivas una petición sin firma puede seguir adelante: el endpoint no debe exponerse como frontera confiable en ese modo.
- Hallazgo directo prioritario: `POST /api/internal/locks/:phone/release` libera el lock sin autorización visible en el manejador consultado.
- También se observaron endpoints de administración de flujos, equipo, enlaces, triggers y webhooks de pago sin una comprobación de autorización visible en sus manejadores respectivos. Antes de exponerlos se requiere autenticación/autorización centralizada, aislamiento por tenant, validación de payload, límites de tamaño y auditoría.
- La pasarela consultada puede crear enlaces y cambiar estado local, pero esta revisión no encontró evidencia suficiente para declarar un cobro/callback productivo. No afirmar pasarelas reales verificadas.
- `FICHA.md` sigue afirmando “FASE DE DISEÑO EXCLUSIVO (Sin código funcional implementado)” y conserva rutas antiguas; es documentación obsoleta frente al README/árbol actual.

### WHATSPOWER-ULTRA — Nivel 3

- El HEAD incluye dispatch HTTP a daemon CRM con `allowRealDispatch: false` por defecto y un adaptador Meta Cloud que entra en mock si falta el token o está activado `META_MOCK_MODE`; el ID de teléfono por defecto es placeholder.
- En `src/server.ts` se observaron rutas de listar/crear/pausar/reanudar/cancelar campañas, crear campañas, ingesta de leads, atribución y herramientas de neuromarketing, sin controles de autenticación visibles en los manejadores consultados. Es un bloqueo prioritario antes de exponerlo a una red no confiable.
- `WebhookLeadIngester` valida y normaliza teléfono, consulta supresión y usa una clave de idempotencia al insertar ítems de campaña, pero el manejador HTTP no valida firma/autenticación del webhook en el código leído. Requiere autorización, consentimiento y aislamiento tenant.
- Tracking guarda IP, user-agent y opcionalmente teléfono del contacto; definir minimización, retención, acceso y aviso de privacidad.
- Neuromarketing se implementa como motor de reglas/catálogos heurísticos; no es evidencia de eficacia psicológica o uplift de conversión demostrado.

## Arquitectura e integración

- Nivel 1: CRM-ALBRA conserva los datos comerciales y el servicio/daemon autorizado de despacho.
- Nivel 2: WHATSPOWER opera los flujos 1:1, locks, handoff y herramientas de equipo.
- Nivel 3: WHATSPOWER-ULTRA administra campañas, cola, cadencia, supresión, atribución y adaptadores de salida.
- Los repositorios siguen separados; no duplicar la base comercial ni crear un socket WhatsApp propio en Nivel 3.
- Contratos/adaptadores presentes no prueban integración E2E, conexión a runtime real, entrega de WhatsApp ni cobro real. Esta revisión no ejecutó tests ni peticiones funcionales.
- No se autorizaron ni realizaron envíos. Mantener `ALLOW_REAL_DISPATCH=false`, campañas reales apagadas y cualquier auto-respuesta bajo autorización explícita.

## Acción recomendada

1. Priorizar autorización autenticada de las rutas WHATSPOWER (en particular liberar lock, CRUD de flujos/equipo, pagos y triggers) y de las rutas de campañas/webhooks de WHATSPOWER-ULTRA.
2. Hacer fallar cerrado si falta el secreto HMAC; aplicar comparación de firma segura y ventana de timestamp/replay.
3. Resolver la divergencia entre D.5/E.2.R local y `CRM-ALBRA/main`.
4. Actualizar la ficha histórica de WHATSPOWER.
5. Correr suites en clean room y luego pruebas integradas con transporte no-op; cualquier envío real requiere autorización separada.

## Estado de evidencia

- **VERIFIED:** hashes HEAD, timestamps de commits, existencia de archivos y contenido estático consultado en los repos públicos.
- **VERIFIED DOCUMENTATION / REPORTED:** pruebas o resultados afirmados por commits/informes que no se reejecutaron.
- **NOT VERIFIED:** builds/tests en esta pasada, runtime desplegado, integración E2E, entrega externa de WhatsApp, Meta Cloud real, cobro/callbacks productivos y estado de la máquina local.

No guardar secretos en CONTEXT. La actualización documenta evidencia pública de solo lectura y conserva las restricciones del Bootstrap.
