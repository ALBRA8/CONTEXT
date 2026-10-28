# Browser Factory — auditoría estática del repositorio (2026-10-28)

## Alcance y nivel de evidencia

- Fuente: `ALBRA8/BROWSER-FACTORY`, rama `main` y rama de corrección `security/remove-hardcoded-proxy-credentials`.
- Revisión directa de `README.md`, `FICHA.md`, `app/index.html`, `app/app.js`, `app/server.mjs`, `.gitignore`, `iniciar.bat` y `data/perfiles.json`.
- `package.json` no existe en la raíz consultada (GitHub respondió 404); no se encontró una suite de pruebas declarada por ese archivo.
- Tipo: auditoría estática de código y documentación. No se ejecutó la aplicación en el PC del propietario, no se lanzó Chrome, no se probaron proxies reales y no se verificó el comportamiento de plataformas externas.

## Qué es hoy (implementado en el código visible)

Browser Factory es una aplicación local Node.js + HTML/CSS/JavaScript que sirve una interfaz en `127.0.0.1:3300`. El servidor guarda perfiles en `data/perfiles.json`, sesiones de Chrome en `data/sesiones/<id>` y proxies en `data/proxies.json` (este último está ignorado por Git). Entre las operaciones visibles:

- Consultar estado del sistema y disponibilidad de Chrome.
- Crear, editar y borrar perfiles/canales.
- Asignar proxy por perfil.
- Abrir perfiles aislados de Chrome mediante `--user-data-dir`.
- Abrir TikTok, YouTube Studio e Instagram; también ofrece una ventana de tamaño móvil con user-agent móvil.
- Asignar un nombre/ruta de video pendiente, marcarlo como publicado y conservar un historial manual.
- Apagar el servidor desde la interfaz.

La publicación no está automatizada: el código abre la página de la plataforma y el usuario realiza la carga; “Marcar como publicado” solo registra la confirmación manual. No se observó una integración API oficial de publicación ni una conexión implementada con YOUTUBE-AUTOMATION/HANDS/EEC en los archivos revisados.

## Hallazgos

### P0 — Credenciales de proxy expuestas en código (corrección propuesta, no cerrada)

La rama de seguridad elimina la configuración de proxy con credenciales precargadas y cambia las respuestas de `GET /api/proxies` y `POST /api/proxies` para no exponer `pass` ni `raw`. También elimina CORS abierto y rechaza orígenes HTTP distintos de la propia interfaz local. En la revisión posterior se añadió validación del ID de perfil antes de guardarlo o usarlo como ruta de sesión, y serialización segura de valores al generar el código JavaScript de la extensión de proxy.

PR: [#1](https://github.com/ALBRA8/BROWSER-FACTORY/pull/1), borrador abierto, no fusionado. Se añadieron `test/security-regressions.test.mjs`, `test/server-smoke.test.mjs` y `.github/workflows/node-checks.yml`. GitHub Actions ejecutó los chequeos de sintaxis de servidor/frontend y las pruebas de regresión estática; el workflow `Node checks` terminó en **success** en el commit `5c0b3c11260d4f2c8ae06350dd500de360838bd3`. La prueba de humo HTTP se añadió después de esa ejecución y todavía no hay una ejecución confirmada que la incluya.

Una revisión limitada de los commits accesibles detectó patrones compatibles con credenciales en el diff del commit inicial `042aa3b021f34dda52d0917811e57718d9292587`; los valores no se reproducen en este informe. Esto no equivale a un análisis exhaustivo de todos los refs, forks o copias.

**Pendiente de cierre:** rotar/revocar la credencial en el proveedor, evaluar limpieza coordinada del historial, validar el flujo de alta/edición de proxies en el equipo real y probar Chrome/proxies reales. Los tests actuales son sintácticos y de regresión estática: no demuestran que la app arranque en el PC, que Chrome acepte la extensión o que los proxies funcionen.

### P1 — La API local modifica estado y no tiene autenticación propia

El servidor está enlazado a `127.0.0.1`, lo que reduce la exposición de red. Sin embargo, las rutas pueden crear/borrar perfiles y proxies, marcar publicaciones y apagar el servicio sin token de autenticación. La rama de seguridad añade comprobación de `Origin` y elimina CORS abierto, una mitigación de solicitudes desde páginas web; no equivale a autenticación y no protege frente a procesos locales no confiables. No se verificó protección frente a DNS rebinding ni validación de `Host`.

### P1 — El ID del perfil se usa para construir una ruta de disco sin validación visible

El endpoint de creación acepta `data.id` y el lanzamiento lo usa en `path.join(SESIONES_DIR, id)`. No se encontró una validación explícita del formato del identificador en `main`. Debe restringirse a un identificador seguro (por ejemplo, letras, números, guion y guion bajo) y rechazarse cualquier valor fuera del formato antes de guardar o usarlo como ruta.

### P1 — El archivo de perfiles está versionado

`data/perfiles.json` está presente en el repositorio y contiene nombres de canales, etiquetas, videos pendientes, asignación de proxy y marcas de tiempo de uso. `.gitignore` ignora sesiones y `data/proxies.json`, pero no `data/perfiles.json`. Esto expone datos operativos y puede ocasionar conflictos o pérdida de datos al actualizar clones si se decide reemplazar/eliminar el archivo sin una migración segura. Antes de retirarlo de Git, diseñar una migración que preserve los datos locales y añadir un archivo de ejemplo sanitizado.

### P1 — La extensión de autenticación de proxy usa Manifest V2

`setupProxyExtension` genera una extensión de Chrome con `manifest_version: 2`. La compatibilidad con Manifest V2 depende de la versión y política de Chrome instalada; no se verificó en el equipo del propietario. La ruta de proxy autenticado debe probarse en la versión objetivo y, si no es compatible, migrarse a una alternativa soportada antes de darla por funcional.

### P2 — La ficha, el README y la interfaz no están alineados

- `FICHA.md` describe perfiles con plataforma TikTok/YouTube y un selector de plataforma; el formulario actual no presenta ese selector y el objeto de perfil guardado no conserva un campo `plataforma`.
- La interfaz y el servidor abren también Instagram y un modo de ventana móvil.
- La ficha promete selector de archivo/miniatura y notas; el formulario visible solo acepta una cadena de video pendiente.
- La ficha y el README describen funcionalidades como especificación/uso, pero no constituyen evidencia de pruebas ni de automatización de publicación.

### P2 — No existe un modelo de identidad digital por cuenta

El modelo visible de perfil incluye nombre, etiquetas, proxy, video pendiente, historial, creación y último inicio. No incluye identificador de cuenta por plataforma, correo asociado, teléfono de recuperación, número de WhatsApp asignado, estado de verificación/2FA ni relación explícita entre una identidad y varias plataformas. Esto confirma que el vínculo correo + teléfono solicitado por el propietario todavía no está implementado.

### P2 — Validación de entrada y límites de solicitud incompletos

Los handlers acumulan el cuerpo HTTP en memoria sin límite explícito y aceptan datos de perfil/proxy con validación limitada. El generador de la extensión inserta valores de configuración del proxy en código JavaScript generado. Deben establecerse límites de tamaño, validar protocolo/host/puerto/campos y escapar correctamente los valores antes de generar la extensión.

## Matriz de estado

| Capacidad | Estado observado |
|---|---|
| UI local y API Node.js | Implementado en código; ejecución real no verificada aquí |
| Perfiles aislados de Chrome | Implementado en código; no probado en el PC |
| Gestión de proxies | Implementada en código; credenciales seguras y compatibilidad no certificadas |
| Asignación e historial manual de videos | Implementado en código |
| Publicación automática en redes | No observada |
| Identidad correo/teléfono/WhatsApp por cuenta | No implementada en el código revisado |
| Integración con YOUTUBE-AUTOMATION/HANDS/EEC | No verificada/no observada en el código revisado |
| Tests automatizados | GitHub Actions: chequeos de sintaxis y regresión estática en verde; runtime real no probado |
| Producción/uso con cuentas reales validado | No verificado |

## Orden de trabajo recomendado

1. Rotar credenciales expuestas y revisar el historial de Git.
2. Verificar los chequeos CI posteriores al merge y completar validación local antes de dar seguridad por cerrada.
3. Completar límites de tamaño de cuerpo y validación estricta de protocolo/host/puerto; ya se añadió validación del ID de perfil y serialización segura del código de extensión.
4. Proteger datos locales: migración y plantilla de perfiles, sin borrar datos del propietario.
5. Probar el lanzamiento de Chrome y proxies, incluyendo compatibilidad de la extensión.
6. Alinear README/FICHA con las funciones reales.
7. Diseñar el modelo de identidad digital (correo, teléfono de recuperación, WhatsApp, plataforma y estado de seguridad) como una fase posterior, con secretos fuera de Git y sin guardar contraseñas/códigos en texto plano.

## Conclusión

Browser Factory no es solo una idea: hay una aplicación real en el repositorio. El código demuestra gestión local de perfiles, sesiones de Chrome, proxies y un registro manual de publicaciones. No demuestra publicación automática, integración con otros sistemas ni validación de producción. Los cambios de seguridad y pruebas quedaron integrados en `main` mediante el PR [#1](https://github.com/ALBRA8/BROWSER-FACTORY/pull/1), commit `98ebac48be8e07a7497949a09f75aeea12c01e1a`. GitHub Actions pasó en la versión anterior de las pruebas estáticas; no se confirmó una ejecución CI para el commit de merge ni para la prueba de humo HTTP añadida al final. Siguen pendientes la rotación/revocación de credenciales, evaluar limpieza coordinada del historial y las pruebas de runtime con Chrome/proxies reales.
