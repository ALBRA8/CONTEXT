# Decisión arquitectónica — primer despliegue oficial de ALBRA: EEC mediante INSTALADOR
Fecha: 2026-10-24
Estado: DECISIÓN DE ARQUITECTURA REGISTRADA / INTEGRACIÓN AÚN NO VERIFICADA

## Decisión
PANEL-DE-CONTROL (EEC / Mission Control) será el **primer proyecto oficial que se despliegue mediante ALBRA8/INSTALADOR**. El objetivo no es simplemente instalar la interfaz del panel: es usarlo como primer piloto operativo para validar el circuito de control y ciclo de vida entre los componentes centrales de ALBRA.

Esta decisión fija el orden de implementación; no afirma que la integración ya exista ni que el despliegue haya sido ejecutado.

## Motivo
EEC es el plano de control del ecosistema: coordina tareas, agentes, aprobaciones, eventos y supervisión. INSTALADOR conserva la autoridad sobre el ciclo de vida de los productos: instalación, configuración, inicio, parada, actualización y recuperación. Probarlos juntos primero permite validar sus fronteras antes de incorporar agentes en lote.

## Secuencia del piloto
1. Revisar el estado actual de ambos repositorios y sus contratos; no reutilizar auditorías antiguas como si describieran el HEAD actual.
2. Cerrar las brechas de autenticación, autorización, identidad de servicio y exposición de rutas EEC antes de habilitar operaciones sensibles.
3. Desplegar EEC desde un entorno administrativo externo mediante INSTALADOR. El primer arranque no puede depender de que el propio EEC esté funcionando.
4. Verificar proceso real, identidad, persistencia, health check real, logs y evidencia de instalación/arranque. Un estado en base de datos o una UI no demuestra por sí solo ejecución real.
5. Conectar EEC con el contrato de operaciones autenticado y versionado de INSTALADOR. INSTALADOR sigue siendo la autoridad del lifecycle; EEC solicita operaciones autorizadas y observa sus resultados, sin duplicar el runtime.
6. Incorporar un **agente piloto pequeño, determinista y real** para comprobar que EEC descubre/gestiona un ejecutor externo, despacha una tarea real, recoge resultado y trazabilidad, y puede supervisar su salud y parada.
7. Probar fallos, timeouts, cancelación, reinicio, permisos, actualización y rollback antes de ampliar la incorporación de agentes.
8. Solo después del E2E satisfactorio, ampliar por lotes a otros agentes. Windows y VPS se validan según sus propios gates; no se consideran soportados solo porque exista un adapter o contrato.

## Fronteras obligatorias
- **INSTALADOR:** lifecycle real y estado autoritativo de instalación/runtime.
- **EEC:** control plane, coordinación, aprobaciones, tareas, observabilidad y solicitud de operaciones autorizadas.
- **Agente piloto:** carga de prueba para demostrar ejecución externa real; no se convierte en otro plano de control.
- **AGENTE-GENERAL:** Seed/Host transversal; no sustituye a EEC ni a INSTALADOR.
- **ALBRA8/CONTEXT:** documentación institucional independiente, fuera del runtime y del registro de agentes de EEC.

## Reglas de seguridad y evidencia
- No confiar en roles, tipo de solicitante o identidad enviados libremente por el cliente.
- Aplicar autenticación, autorización/RBAC, identidad de servicio y auditoría a operaciones de lifecycle.
- Denegar por defecto si salud, identidad, permisos o compatibilidad son desconocidos.
- Separar SIMULATED, UNIT/CI, CLEAN-ROOM, E2E real y producción; no llamar LIVE PASS a pruebas simuladas ni a resultados meramente reportados.
- Evitar autoinstalación circular: el primer despliegue de EEC lo inicia un entorno administrativo externo.

## Criterio de aceptación
La decisión solo se considera técnicamente validada cuando existe evidencia reproducible de este flujo completo: **INSTALADOR despliega EEC → EEC queda sano y observable → EEC solicita una operación autenticada a INSTALADOR → EEC ejecuta una tarea mediante un agente real → se verifican resultado, identidad, logs y trazabilidad → se prueba parada y recuperación**.

## Estado conocido al registrar la decisión
- INSTALADOR: el HEAD consultado el 2026-10-24 es `2313afea75452554b79d305bafd7091e77db3e00`; el commit documenta runtime real de procesos, fuentes GitHub fijadas, cadena de confianza Ed25519 y contrato de operaciones EEC. Las pruebas 18/18 son reportadas por el commit y no se repitieron independientemente en esta actualización.
- PANEL-DE-CONTROL: el HEAD consultado es `71b46ef5a58795aa032474298829befa93031020`; mantiene providers de simulación por defecto según la auditoría previa. Seguridad de rutas e integración real deben volver a verificarse contra el HEAD vigente antes de conectar.
- INSTALADOR ↔ EEC: contrato documentado en INSTALADOR, pero integración end-to-end real aún no verificada.
- Windows: ejecución real no certificada en esta revisión; VPS sigue declarado no implementado en el adapter inspeccionado.

Fuentes:
- [INSTALADOR](https://github.com/ALBRA8/INSTALADOR/commit/2313afea75452554b79d305bafd7091e77db3e00)
- [PANEL-DE-CONTROL](https://github.com/ALBRA8/PANEL-DE-CONTROL/commit/71b46ef5a58795aa032474298829befa93031020)
- [Auditoría de integración del 2026-10-22](../03_AUDITS/INSTALLER_EEC_AGENT_GENERAL_INTEGRATION_AUDIT_2026-10-22.md)
