# ALBRA — Integración INSTALADOR ↔ PANEL DE CONTROL (alcance inmediato)

- Estado: **PROPOSED / SCOPE CONFIRMED BY OWNER / DESIGN ONLY**
- Fecha: 2026-11-12
- Alcance de escritura documental: \`ALBRA8/CONTEXT\`
- Repositorios fuente consultados, no modificados: \`ALBRA8/INSTALADOR\` y \`ALBRA8/PANEL-DE-CONTROL\`.

## 1. Decisión de alcance

La instrucción del propietario para esta etapa es **integrar funcionalmente el INSTALADOR con el PANEL DE CONTROL mediante el contrato EEC**.

“Integrar/fusionar” significa que ambos productos trabajen coordinadamente mediante una interfaz EEC versionada. **No significa** unir sus repositorios, convertirlos en un monorepo ni trasladar el código de uno al otro.

- **INSTALADOR:** autoridad que valida y ejecuta operaciones reales de instalación y ciclo de vida.
- **PANEL DE CONTROL:** interfaz de administración; solicita operaciones, presenta progreso y refleja resultados confirmados.
- **EEC:** contrato compartido para solicitudes, respuestas, autenticación, seguimiento, errores e idempotencia.
- **CONTEXT:** documentación, decisiones y evidencia; no participa en la ejecución.

Esta especificación fija el alcance inmediato, pero no convierte automáticamente cada detalle técnico en una decisión aprobada. Los detalles marcados como propuestos deben validarse contra el código y aprobarse antes de implementarse.

## 2. Fuera de alcance ahora

No forman parte de esta etapa:

- Preparar o integrar CHISMOSO como agente instalable.
- Construir el catálogo general de agentes o incorporar otros agentes.
- Ejecutar una instalación en PC/VPS, desplegar infraestructura del propietario o certificar producción.
- Publicar imágenes OCI/GHCR, configurar dominios, proxy o infraestructura externa.
- Fusionar físicamente los repositorios o cambiar su distribución.
- Hacer una auditoría general de todo el ecosistema ALBRA.

Esos trabajos podrán tratarse después de cerrar y verificar la integración INSTALADOR ↔ PANEL.

## 3. Resultado funcional esperado

1. El PANEL autentica y envía una solicitud EEC al INSTALADOR.
2. El INSTALADOR valida contrato, autorización, parámetros e idempotencia.
3. El INSTALADOR ejecuta la operación o la rechaza con un error tipado.
4. Si es asíncrona, devuelve aceptación y un \`runId\` válido.
5. El PANEL registra la operación como aceptada/en ejecución, nunca como terminada prematuramente.
6. El PANEL consulta el run y actualiza el registro persistente con su estado y resultado terminal.
7. STATUS/HEALTH reflejan información del INSTALADOR, no estados simulados.
8. Timeouts y reintentos no crean operaciones duplicadas ni falsos éxitos.

## 4. Contrato y responsabilidades

| Componente | Responsabilidad | Límite |
|---|---|---|
| PANEL | Cliente EEC, persistencia de ManagedOperation, polling y presentación | No ejecuta Docker ni manipula directamente el runtime |
| INSTALADOR | Autorización, validación, ejecución, persistencia de runs y resultados | No depende del navegador para controlar procesos |
| EEC | Contratos versionados, correlación, estados, errores y seguridad | No es un segundo motor de ejecución |
| CONTEXT | Decisiones, especificaciones y evidencia | No es parte del plano de ejecución |

## 5. Sobre de solicitud

La reconciliación EEC V1 registra estos campos:

\`\`\`json
{
  "contractVersion": "1.0.0",
  "requestId": "uuid",
  "operation": "INSTALL",
  "productId": "product-id",
  "params": {},
  "nonce": "random-unique-value",
  "issuedAt": "ISO-8601 timestamp",
  "expiresAt": "ISO-8601 timestamp"
}
\`\`\`

La credencial viaja en \`Authorization: Bearer\`; nunca debe incluirse en el cuerpo, logs, UI ni auditorías.

- \`requestId\`: identidad estable de la intención lógica; se reutiliza en reintentos para idempotencia.
- \`nonce\`: valor anti-replay de un intento autenticado; requiere validación y almacenamiento durable.
- \`runId\`: identifica la ejecución del INSTALADOR; no equivale a requestId.
- \`operationId\`: identificador interno del registro del PANEL; no sustituye los IDs del protocolo.

Los formatos, obligatoriedad y límites exactos deben quedar reflejados en esquemas compartidos y pruebas. No introducir campos alternativos sin versionar el contrato.

## 6. Respuesta y seguimiento

La reconciliación previa indica que los datos operativos del INSTALADOR están dentro de \`result\`. El PANEL debe leer \`result.runId\`, \`result.state\`, \`result.health\` y \`result.detail\` cuando correspondan, y validar que versión, requestId, operación y producto coincidan con la solicitud.

Ejemplo conceptual de aceptación:

\`\`\`json
{
  "contractVersion": "1.0.0",
  "requestId": "uuid",
  "accepted": true,
  "operation": "INSTALL",
  "productId": "product-id",
  "result": {
    "runId": "run-id"
  }
}
\`\`\`

**Aceptada no significa terminada.** El PANEL debe conservar un estado no terminal y consultar el endpoint existente \`GET /api/runs/{id}\`.

La auditoría estática del 2026-11-12 reporta que ese endpoint devuelve un \`LifecycleRunDTO\` directo con \`id\`, \`status\`, \`steps\`, \`error\`, \`startedAt\` y \`finishedAt\`. Ese DTO real debe guiar el mapper del PANEL; no se debe implementar el DTO ilustrativo del documento de diseño como si fuera la respuesta real.

El PANEL debe comprobar que el ID consultado es el run esperado y traducir explícitamente los estados reales del INSTALADOR. Un estado desconocido nunca se convierte silenciosamente en éxito.

## 7. Ciclo de vida persistente

Estados mínimos del registro del PANEL:

- \`REQUESTED\`: solicitud creada localmente.
- \`ACCEPTED\`: el INSTALADOR confirmó recepción y aceptación.
- \`RUNNING\`: el run está ejecutándose.
- \`SUCCEEDED\`: resultado exitoso terminal confirmado.
- \`FAILED\`: fallo terminal confirmado.
- \`CANCELLED\`: cancelación terminal confirmada, si está soportada.

Reglas:

1. \`acceptedAt\` se registra al aceptar; no es fecha de finalización.
2. \`finishedAt\` se obtiene del resultado terminal del run cuando exista.
3. \`resolvedAt\` solo se asigna al alcanzar un estado terminal conforme a la política aprobada.
4. El polling actualiza el mismo \`ManagedOperation\`; no crea un registro nuevo por consulta.
5. Timeout de red es resultado ambiguo, no prueba de fallo ni de éxito. Reconciliar por el identificador durable antes de reintentar.
6. Estados terminales no retroceden por respuestas tardías.
7. Persistir correlación, timestamps, estado y error normalizado; nunca tokens ni secretos.

La auditoría estática del 2026-11-12 reporta un defecto potencial de \`resolvedAt\` prematuro y falta de actualización de \`ManagedOperation\` durante el polling inspeccionado. Son objetivos pendientes, no correcciones verificadas.

## 8. Seguridad, idempotencia y consistencia

Criterios obligatorios de aceptación:

- Autenticación en todas las rutas EEC, incluidas consultas de runs, salvo control perimetral explícito, documentado y probado como complemento.
- Autorización por operación y producto.
- Validación de \`issuedAt\` y \`expiresAt\`, incluyendo tolerancia de reloj y rechazo de fechas futuras fuera de ventana.
- Nonces de un solo uso persistidos de forma durable y compartida; un Map en memoria no satisface el requisito.
- Idempotencia durable por \`requestId\` y huella del payload. Mismo ID con payload distinto debe rechazarse.
- Locks/transiciones atómicas para operaciones concurrentes sobre el mismo producto.
- Redacción de credenciales y secretos en respuestas, logs y auditoría.
- Semánticas diferenciadas para errores HTTP, rechazos de negocio, validación y resultados ambiguos por timeout.
- Ningún error de seguimiento se convierte por defecto en éxito.

La revisión estática reporta nonce anti-replay en memoria y ausencia de validación suficiente de fecha futura en \`issuedAt\`. Los GET de runs revisados no muestran autenticación local; la exposición efectiva depende de firewall, proxy y topología, no verificados aquí. Esto no demuestra exposición pública ni explotación.

## 9. Operaciones

El contrato contempla: \`INSTALL\`, \`START\`, \`STOP\`, \`RESTART\`, \`STATUS\`, \`HEALTH\`, \`UPDATE\`, \`ROLLBACK\`, \`BACKUP\`, \`RESTORE\`, \`UNINSTALL\` y \`DIAGNOSTICS\`.

Una operación solo se considera soportada cuando existe handler real, autorización, validación, resultado y prueba de contrato en ambos lados. Declararla en un enum o mostrarla en UI no basta. Cada operación debe declarar si es síncrona o asíncrona por tipo de despliegue.

La auditoría previa reporta que \`UNINSTALL\` contenedorizado puede responder sin \`runId\` pese a estar documentado como asíncrono. Debe resolverse con semántica coherente.

## 10. Pruebas de aceptación

1. Contratos compartidos para solicitudes, respuestas y errores.
2. Rechazo de versión, operación, producto y parámetros inválidos.
3. Rechazo de credencial ausente/incorrecta y autorización insuficiente.
4. Rechazo de nonce repetido, expirado y timestamp futuro inválido.
5. Idempotencia: mismo requestId/payload no duplica; mismo requestId/payload distinto se rechaza.
6. INSTALL: aceptación con runId, polling y transición terminal correcta.
7. acceptedAt no resuelve; resolvedAt/finishedAt solo al finalizar.
8. Polling actualiza el mismo ManagedOperation y maneja estados desconocidos, 404, 401, 5xx y timeout.
9. Recuperación tras reinicio sin perder idempotencia ni estado durable.
10. STATUS/HEALTH reflejan estado real.
11. Concurrencia de solicitudes sobre el mismo producto.
12. Regresión de operaciones síncronas/asíncronas, especialmente UNINSTALL.
13. Typecheck, lint, tests y build de ambos repositorios, anclados a commits concretos.

La prueba con Docker o infraestructura real es un gate separado si requiere entorno externo. No declarar certificación completa hasta ejecutar ese gate.

## 11. Secuencia de trabajo

1. Mantener esta especificación como referencia de alcance y diseño.
2. Verificar HEADs y contratos actuales antes de codificar.
3. Cerrar decisiones técnicas y matriz de compatibilidad.
4. Implementar persistencia de ciclo de vida/polling en PANEL y controles de idempotencia/anti-replay/autorización en INSTALADOR, coordinando contratos.
5. Ejecutar pruebas contractuales y suites de ambos repositorios.
6. Revisar diffs, migraciones, logs y evidencia de cada gate.
7. Cerrar el hito solo con evidencia verificable.

Por la regla vigente del Bootstrap, este flujo permite escrituras únicamente en CONTEXT. Este documento no autoriza ni ejecuta cambios en INSTALADOR o PANEL; la implementación requiere un flujo de trabajo con autorización de escritura para esos repositorios.

## 12. Criterio de cierre

La integración se considera terminada cuando el PANEL solicita y sigue una operación real, conserva su estado durante toda la ejecución, recupera el resultado terminal del INSTALADOR y demuestra autenticación, idempotencia y trazabilidad mediante pruebas.

No basta con que existan documentos compatibles, que la solicitud sea aceptada o que el PANEL muestre “completado” sin evidencia terminal del INSTALADOR.

## 13. Fuentes relacionadas

- [Reconciliación EEC Ops V1](ALBRA_EEC_OPS_CONTRACT_V1_RECONCILIATION.md).
- [Ciclo de vida y semántica de errores EEC Ops V1](ALBRA_EEC_OPS_LIFECYCLE_AND_ERROR_SEMANTICS_V1.md).
- [Auditoría de compatibilidad contra HEADs actuales — 2026-11-12](../03_AUDITS/ALBRA_EEC_CURRENT_HEAD_COMPATIBILITY_AUDIT_2026-11-12.md).

**Límite de evidencia:** esta especificación consolida el alcance solicitado y los hallazgos documentales/estáticos disponibles. No constituye implementación, prueba ejecutada, aprobación de producción ni certificación independiente.
