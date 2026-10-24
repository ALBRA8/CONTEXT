# ALBRA — Reauditoría INSTALADOR × EEC/PANEL-DE-CONTROL
Fecha: 2026-10-24
Estado: REVISIÓN DOCUMENTAL DIRECTA — NO CERTIFICACIÓN E2E

## 1. Pregunta

Determinar si el estado actual permite tratar al EEC/PANEL-DE-CONTROL como el primer producto real que debe desplegar el INSTALADOR y, antes de ello, si existe ya una integración contractual/operativa suficiente.

## 2. INSTALADOR — estado observado

HEAD consultado: `2313afea75452554b79d305bafd7091e77db3e00`.

El commit V2 declara:

- runtime real de procesos del sistema operativo;
- instalación desde releases/tags/commits con rechazo de referencias de rama;
- checksum/procedencia de fuente;
- trust chain Ed25519;
- contrato de operaciones EEC v1.0.0 con bearer, expiración, anti-replay nonce y auditoría;
- 18/18 PASS reportados con procesos reales;
- Win32 implementado pero UNTESTED;
- VPS y CA de firma pendientes;
- ejecución de scripts aprobados diferida.

La documentación `docs/EEC-CONTRACT.md` y el código `src/lib/albra/contracts/eec.ts` son explícitos en un punto arquitectónico clave: **el EEC todavía no está construido dentro del INSTALADOR; el repositorio contiene el contrato que una EEC futura utilizaría para solicitar operaciones al INSTALADOR.**

Esto significa que “contrato EEC verificado” no equivale a “EEC instalado mediante INSTALADOR”.

## 3. PANEL-DE-CONTROL — estado observado

HEAD consultado: `71b46ef5a58795aa032474298829befa93031020`.

El commit `19b4472c62a73a17ad14fe0206e35777b443a069` describe EEC v1.0 como control plane completo, con:

- fuente de verdad operacional;
- pipeline VALIDACIÓN → AUTORIZACIÓN → POLÍTICA → DISPATCH;
- providers/adapters neutrales;
- adaptadores `local-sim` honestos;
- UI y pruebas de aceptación;
- Gateway Caddy en `:81`.

El commit posterior `71b46ef5...` corrige la exclusión accidental de `local-sim.ts` por `.gitignore`.

## 4. Hallazgo crítico de seguridad del EEC

El endpoint `src/app/api/eec/commands/route.ts` acepta el cuerpo JSON y pasa directamente a `dispatchCommand()` los campos `requesterType` y `requesterId` suministrados por el cliente. En el archivo revisado no aparece autenticación HTTP previa al despacho.

El pipeline interno sí contiene controles de autorización para `requesterType === 'AGENT'`, pero esos controles dependen del `requesterType/requesterId` que llega en el body. Un cliente que pueda invocar directamente la ruta puede declarar `HUMAN` y no pasa por el mismo gate de permisos de agente.

El endpoint `src/app/api/eec/state/route.ts` tampoco muestra autenticación en su handler.

### Dictamen

**P0/P1 de seguridad, según superficie de exposición:** antes de exponer el EEC fuera de un entorno estrictamente local/controlado, debe existir autenticación y autorización de transporte/API independientes del campo `requesterType` del body. No debe confiarse en identidad declarada por el cliente como frontera de seguridad.

No se ejecutó explotación ni se afirma que el endpoint sea públicamente accesible desde Internet. El hallazgo es de revisión de código y depende de la exposición real de Caddy/host/firewall.

## 5. Caddy — riesgo adicional

`Caddyfile` escucha en `:81` y contiene una regla que usa el query parameter `XTransformPort` para seleccionar dinámicamente el puerto destino de `reverse_proxy`.

Esto no demuestra por sí solo una vulnerabilidad explotable en el despliegue real, pero sí es una superficie que debe cerrarse/restringirse antes de exposición remota: el proxy no debería permitir que una entrada arbitraria del cliente seleccione puertos localhost sin una allowlist estricta.

## 6. Respuesta a la estrategia “primer producto del INSTALADOR = EEC”

La idea arquitectónica **sí tiene sentido como primer despliegue real del INSTALADOR**, porque permite validar el lifecycle del propio control plane sobre el componente que gobierna el ecosistema.

Pero hoy no debe marcarse como E2E logrado.

La frontera correcta es:

`INSTALADOR` → instala/gestiona → `PANEL-DE-CONTROL / EEC`

y no:

`INSTALADOR` → “ya integra EEC”

Actualmente existe el **contrato** INSTALADOR ↔ EEC y existe un **EEC implementado en PANEL-DE-CONTROL**, pero falta demostrar el ciclo completo en un entorno limpio:

1. fuente/version exacta del PANEL-DE-CONTROL;
2. instalación mediante INSTALADOR;
3. preparación de dependencias;
4. configuración/secrets sin `.env` manual inseguro;
5. arranque como proceso real;
6. health autenticado;
7. identidad PID/proceso;
8. operación EEC funcional;
9. persistencia/backup/restore;
10. stop/restart/update/rollback;
11. evidencia de auditoría;
12. autenticación/autorización real del EEC;
13. verificación de exposición de Caddy/proxy;
14. clean-room reproducible.

## 7. Veredicto actual

| Elemento | Estado |
|---|---|
| INSTALADOR V2 como lifecycle engine | **IMPLEMENTADO / REPORTADO 18/18** |
| Contrato INSTALADOR ↔ EEC | **IMPLEMENTADO Y DOCUMENTADO** |
| EEC/PANEL-DE-CONTROL | **IMPLEMENTADO, con local-sim y control plane** |
| INSTALADOR instalando EEC de extremo a extremo | **NO DEMOSTRADO** |
| EEC autenticado como servicio externo | **NO DEMOSTRADO; código de rutas revisadas no muestra auth HTTP** |
| Exposición segura remota | **NO CERTIFICADA** |
| Primer producto recomendado para validar el INSTALADOR | **EEC/PANEL-DE-CONTROL: sí como estrategia de validación, no como E2E ya logrado** |

## 8. Próximo gate

No construir otra capa todavía.

Primero hay que cerrar la frontera existente: **INSTALADOR → PANEL-DE-CONTROL/EEC**.

La siguiente auditoría debe inspeccionar el paquete/fuente que el INSTALADOR usaría para instalar PANEL-DE-CONTROL, su manifest/entitlement, puertos, secretos, health contract, lifecycle hooks y rollback, y después reproducir un clean-room E2E. Hasta entonces, EEC queda en estado **TARGET FOR FIRST INSTALLER DEPLOYMENT / NOT YET E2E-CERTIFIED**.

No se modificaron INSTALADOR ni PANEL-DE-CONTROL durante esta reauditoría.