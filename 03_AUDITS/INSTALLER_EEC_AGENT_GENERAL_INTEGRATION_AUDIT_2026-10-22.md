# Auditoría de integración — INSTALADOR global + EEC + AGENTE-GENERAL
Fecha: 2026-10-22
Estado: AUDITORÍA DE CÓDIGO REMOTO / NO CERTIFICACIÓN DE PRODUCCIÓN

## Objetivo

Determinar si los nuevos repositorios permiten incorporar los agentes ALBRA desde un panel global, instalar sus dependencias y gestionarlos sin abrir una terminal por agente; establecer las fronteras entre INSTALADOR, EEC y AGENTE-GENERAL.

## Repositorios y evidencia examinada

- [ALBRA8/INSTALADOR](https://github.com/ALBRA8/INSTALADOR), rama `main`, último commit consultado: `451d7203cfa9a9ba86026f058130b8c9d11c08ad` — “ALBRA Deployment & Lifecycle System V1.0 — MVP completo y certificado”.
- [ALBRA8/PANEL-DE-CONTROL](https://github.com/ALBRA8/PANEL-DE-CONTROL), rama `main`, último commit consultado: `71b46ef5a58795aa032474298829befa93031020`; incluye `19b4472c62a73a17ad14fe0206e35777b443a069` (“EEC v1.0: control plane completo”) y el arreglo de inclusión de `local-sim.ts`.
- [ALBRA8/AGENTE-GENERAL](https://github.com/ALBRA8/AGENTE-GENERAL), rama `main`, último commit consultado: `69c79dad8337be156bcc60e74b53f98d5a993d54`; incorpora el merge de PB-02 para las skills de YouTube validate/system_status y el harness especializado de YouTube.
- Se inspeccionaron README/package.json, contratos, adapters, runtime, installer engine, package format, health/security docs, EEC boot/domain contracts, adapter local-sim, pipeline de comandos, gateway, rutas API y tests de aceptación relevantes.
- Los repositorios reportan certificaciones en mensajes/documentación, pero esta revisión no ejecutó las suites localmente ni en un clean-room. Las consultas de workflow runs de los commits examinados no devolvieron ejecuciones; por tanto, no se certifica CI ni ejecución física.

## Dictamen ejecutivo

**La arquitectura base está bien encaminada, pero aún NO se deben conectar todos los agentes ni desplegar la plataforma en VPS.** INSTALADOR ya tiene un motor de ciclo de vida y una GUI; EEC ya tiene un control plane con estados, tareas, providers, eventos, auditoría y aprobaciones; AGENTE-GENERAL ya implementa el patrón Seed/Host y ha empezado a consumir un harness especializado. Sin embargo, las interfaces reales todavía no están conectadas de extremo a extremo.

El principal gap no es crear otro instalador ni otro cerebro: es definir y ejecutar un contrato de producto/agente común, implementar providers reales, y cerrar las fronteras de autenticación, ejecución y despliegue.

## 1. INSTALADOR — lo que existe

El repo implementa un sistema web Next.js/Bun con catálogo, contratos Zod, paquetes `.albra`, bóveda, firma/verificación, configuración por scopes, SecretStore, auditoría, runs observables y operaciones de ciclo de vida: INSTALL, CONFIGURE, START, STOP, RESTART, UPDATE, ROLLBACK, DIAGNOSTICS, REPAIR, BACKUP, RESTORE y UNINSTALL.

Evidencia directa:
- `src/lib/albra/contracts/index.ts`: identidad de producto, SemVer, plataformas/arquitecturas, dependencias, configuración, salud, lifecycle y riesgos.
- `src/lib/albra/package/format.ts`: formato `ALBRA-PACKAGE 1.0.0`, SHA-256 y HMAC-SHA256.
- `src/lib/albra/core/installer.ts`: pipeline DISCOVER → PREFLIGHT → PLAN → CONFIRM → DOWNLOAD → VERIFY → PREPARE → INSTALL → CONFIGURE → INITIALIZE → MIGRATE → REGISTER → START → HEALTH → COMPLETE.
- `docs/ARCHITECTURE.md` y `docs/SECURITY.md`: aislamiento por producto, staging/commit/rollback, secretos cifrados AES-256-GCM y redacción defensiva.
- El commit más reciente declara un clean-room con 17/17 PASS y nueve documentos; es una afirmación del commit, no una suite repetida de forma independiente aquí.

### Gaps bloqueantes

1. **No es aún un instalador universal de repositorios/agentes.** El catálogo visible contiene productos piloto CRM y LEADS; KREA/TRADING aparecen reservados por entitlement. No se encontró evidencia de descubrimiento/descarga automática de cualquier repo GitHub ni de incorporación directa de agentes EEC a partir de un manifiesto universal.
2. **El adaptador Windows no está demostrado como implementación de producción completa.** `WindowsLocalAdapter` hereda la implementación local genérica y ajusta la identificación/plataforma. La documentación lo describe como “especificado”. Debe probarse en Windows real antes de certificarlo.
3. **VPS no está implementado.** `SelfHostedDeploymentAdapter.isImplemented()` devuelve `false`; `provisioning`, `remoteExec`, `nginx` y `backupsRemote` aparecen deshabilitados. No afirmar que INSTALADOR despliega en VPS.
4. **El runtime actual no arranca procesos reales.** `src/lib/albra/core/runtime.ts` crea un `runtimeHandle` sintético y cambia el estado persistido a RUNNING/STOPPED, pero no se observa creación/terminación de proceso hijo, servicio Windows ni contenedor. En consecuencia, “START_COMPLETED” no prueba que una aplicación externa esté ejecutándose.
5. **El health check de runtime se apoya en el estado de la base de datos.** Un producto marcado RUNNING puede pasar el check sin demostrar que un proceso responde. Las comprobaciones de puerto también necesitan distinguir puerto libre/ocupado según el proceso esperado.
6. **Confianza de paquetes:** la clave HMAC se genera localmente y se conserva en `keys/package-signing.key`. Para paquetes distribuidos desde un catálogo central hace falta definir quién firma, cómo se protege la clave de firma y cómo se rota/revoca. Una firma HMAC compartida no ofrece el mismo modelo de confianza que una firma asimétrica con clave pública de verificación.
7. `package.json` conserva `db:push --accept-data-loss`. No usar ese comando contra datos valiosos como operación normal de actualización; exigir migraciones versionadas, respaldo y aceptación explícita.

**Conclusión INSTALADOR:** candidato a ser el instalador/gestor de ciclo de vida central, pero aún no equivale a un launcher de agentes productivo ni a un despliegue remoto. Debe evolucionar mediante adapters y contratos, no reescribirse desde cero.

## 2. EEC / PANEL-DE-CONTROL — lo que existe

La EEC está implementada como aplicación de control con fuente de verdad de estado, pipeline de comandos, provider registry, adaptadores, trabajadores, tareas, runs, reuniones, aprobaciones, eventos correlacionados, auditoría, memoria/conocimiento y GUI de 13 vistas.

Evidencia directa:
- README: separación control plane/execution plane; flujo EEC → INTERFACE → ADAPTER → PROVIDER; simulación explícita.
- `src/lib/eec/boot.ts`: registra por defecto `LocalSimAgentAdapter`, `LocalOrchestratorAdapter`, `LocalRuntimeAdapter` y `BuiltinToolsAdapter`.
- `src/lib/eec/domain/contracts.ts`: interfaces de agente, orquestación, runtime, tools y memoria.
- `src/lib/eec/commands/pipeline.ts`: validación, autorización, políticas, idempotencia, riesgo/aprobación y despacho.
- `tests/eec/acceptance.test.ts`: contiene escenarios de aceptación que operan contra SQLite y el pipeline de comandos. La existencia del archivo no sustituye una ejecución actual de los tests.
- El commit `71b46ef` corrige que `local-sim.ts` quede incluido pese al patrón de `.gitignore`; esto resuelve un problema de empaquetado del adapter de simulación, no la conexión a agentes reales.

### Gaps bloqueantes

1. **La configuración por defecto es simulada.** El adapter local avanza tareas de manera determinista y marca la simulación; es útil para validar el control plane, pero no es un agente externo conectado.
2. **No se encontró aún un adapter real del INSTALADOR dentro de EEC ni un registro sincronizado de productos instalados.** EEC y el instalador persisten estados en sus propias bases/modelos; falta definir quién es autoridad para instalación, runtime, salud y versión.
3. **La interfaz de agente exige `tick(ctx)` y health opcional.** Esto es útil para workers compatibles, pero no basta por sí solo para integrar cualquier API/MCP. Hace falta un adapter por protocolo/ejecutor que traduzca la interfaz EEC al contrato de cada agente y normalice timeout, errores, cancelación, outputs y trazas.
4. **Gate de seguridad antes de exponer la EEC:** la ruta `src/app/api/eec/commands/route.ts` acepta `requesterType` y `requesterId` del cuerpo de la petición, con HUMAN como valor por defecto. En `pipeline.ts`, HUMAN tiene un tratamiento de autoridad distinto a AGENT y SYSTEM omite aprobaciones en varios casos. No se observó autenticación/autorización en el propio handler inspeccionado. Antes de exposición remota debe verificarse un middleware/gateway efectivo o añadirse autenticación, RBAC, identidad de servicio firmada y prohibición de confiar en `requesterType` aportado por el cliente.
5. La ruta de estado `src/app/api/eec/state/route.ts` devuelve un snapshot operacional amplio. Debe comprobarse la protección de lectura y la clasificación de campos antes de exponerla fuera de localhost.
6. El modo simulación se activa por defecto salvo configuración explícita, pero no debe confundirse el badge/UI con prueba de ejecución real. Las pruebas LIVE deben incluir un provider externo de prueba, no solo el adapter local.

**Conclusión EEC:** ya existe un control plane real como código, no solo un diseño visual. Aún no está certificada como plano de control de producción ni como administrador real de procesos externos.

## 3. AGENTE-GENERAL — evolución relevante

El README lo define como Seed universal, no como agente de dominio. Eso encaja con mantenerlo como base transversal y evitar convertirlo en un superagente que absorba las especialidades.

Los commits recientes muestran un avance importante: `YoutubeHarnessClient`, tool y skills `skill.youtube.*` con políticas de riesgo/permisos, y después skills de solo lectura `youtube.validate` y `youtube.system_status`. El commit `69c79da` fusionó PB-02 en `main`.

Esto prueba integración especializada en el lado AGENTE-GENERAL/YOUTUBE-HARNESS, no una integración general de todos los repositorios en EEC ni una certificación del instalador. La EEC debe coordinar tareas y observar estado; AGENTE-GENERAL aporta el Seed/Host transversal; los agentes de dominio conservan sus capacidades propias.

## 4. Arquitectura recomendada — fronteras

- **INSTALADOR:** descargar/validar paquetes, instalar/configurar, migrar, versionar, arrancar/detener de verdad, actualizar/rollback, backups, secretos, diagnósticos y salud de los productos.
- **EEC:** catálogo operacional de agentes y runtimes, asignación de tareas, coordinación, aprobaciones humanas, eventos, trazas y visualización. No debería ejecutar instaladores arbitrarios directamente; debe solicitar operaciones al servicio de lifecycle mediante contrato autenticado.
- **AGENTE-GENERAL:** Seed/Host transversal y punto de extensión para agentes compatibles; no reemplaza a la EEC ni absorbe los dominios.
- **Agentes especializados:** siguen siendo repositorios/productos independientes. Cada uno publica metadatos y un adapter de ejecución/health; no necesita incorporar una copia completa del instalador global.
- **CONTEXT:** memoria documental institucional separada. No es un agente, no es un provider EEC y no debe registrarse como miembro de la EEC.

### Contrato mínimo de incorporación propuesto (aún no implementado como estándar compartido)

Cada agente debe declarar, con versión:
- `agentId`, nombre, versión, repositorio y canal de release;
- modo de ejecución soportado (proceso local, servicio local, HTTP, MCP u otro adapter explícito);
- plataforma/arquitectura y dependencias;
- comandos lifecycle realmente soportados;
- endpoint/operación de health que mida el servicio real;
- capacidades, permisos, niveles de riesgo y acciones que exigen HITL;
- configuración requerida con clasificación de secretos;
- timeout, cancelación, idempotencia, formato de outputs y trace/correlation IDs;
- migraciones, backup/restore, política de actualización y rollback;
- pruebas requeridas y nivel de evidencia (UNIT, CI, CLEAN-ROOM, SIMULATED E2E, LIVE, PRODUCTION).

El contrato debe ser un descriptor pequeño versionado, no una copia del código del instalador. La integración debe fallar de forma cerrada si faltan health, identidad, permisos o compatibilidad.

## 5. Secuencia de trabajo recomendada

1. **No conectar todos los agentes aún.** Primero definir el contrato de incorporación común y su esquema versionado.
2. Corregir INSTALADOR para que START/STOP administren procesos reales y health compruebe el proceso/endpoint; conservar el diseño adapter-driven.
3. Cerrar autenticación/RBAC de las rutas EEC y establecer identidad de servicio; no confiar en roles declarados por el cliente.
4. Crear en EEC un adapter autenticado hacia el lifecycle service de INSTALADOR y una fuente clara de verdad para versiones/estado de instalación.
5. Construir un paquete real de un agente piloto, instalarlo desde GUI y probar ciclo completo: instalar → configurar secretos → iniciar proceso → health real → asignar tarea → observar resultado → detener → actualizar/rollback.
6. Solo tras ese E2E, diseñar y probar el adapter Windows de producción y después el destino VPS. VPS no es un gate actual ya implementado.
7. Incorporar otros agentes por lotes según compatibilidad; no exigir que todos compartan stack tecnológico, solo el contrato y el adapter correspondiente.

## Estado final

- INSTALADOR: **MVP de gestión de productos con GUI; agente-universal/Windows runtime/VPS aún NO_VERIFIED o no implementados**.
- EEC: **control plane implementado con providers locales de simulación; integración real externa y seguridad de despliegue pendientes**.
- AGENTE-GENERAL: **Seed/Host con especialización YouTube incorporada recientemente; no es sustituto de EEC ni instalador**.
- Integración INSTALADOR ↔ EEC: **NO IMPLEMENTED / NO E2E VERIFIED**.
- Despliegue VPS: **NO IMPLEMENTED** según el adapter inspeccionado.
- Modificaciones realizadas en esta auditoría: solo documentación de `ALBRA8/CONTEXT`; no se modificó código de los tres repositorios auditados.
