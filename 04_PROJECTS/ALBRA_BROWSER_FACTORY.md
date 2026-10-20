# ALBRA Browser Factory

- Estado: APPROVED FOR PROJECT DESIGN / IN ENGINEERING
- Fecha de registro: 2026-10-16
- Tipo: herramienta independiente de ALBRA; NO es un agente.
- Referencia funcional: Dolphin Anty, usado como referencia para ingeniería inversa funcional y descubrimiento de capacidades.
- Consumidor principal: `ALBRA8/YOUTUBE-AUTOMATION`.

## 1. Qué es

ALBRA Browser Factory será una herramienta propia para organizar, aislar, administrar y desplegar múltiples perfiles/cuentas de plataformas de contenido, inicialmente con foco en YouTube y TikTok.

La motivación es resolver el problema de escala: manejar pocas cuentas puede hacerse manualmente, pero con 100, 200 o más cuentas abrir ventanas, cambiar sesiones y recordar manualmente qué contenido corresponde a qué cuenta se convierte en un cuello de botella y una fuente de errores.

## 2. Qué NO es

- No es un agente.
- No tiene que convertirse en un cerebro adicional de ALBRA.
- No sustituye a `AGENTE-GENERAL`.
- No sustituye a `YOUTUBE-AUTOMATION`.
- No sustituye a HANDS.
- No debe quedar acoplada como código interno del EEC.

## 3. Arquitectura conceptual

```text
                         ALBRA
                           │
                    AGENTE-GENERAL
                seguridad / contratos / gobierno
                           │
                    ┌──────┴──────┐
                    │             │
                  EEC       YOUTUBE-AUTOMATION
                    │             │
                    │      contenido / producción
                    │             │
                    └──────┬──────┘
                           │
                  ALBRA BROWSER FACTORY
                           │
             perfiles / cuentas / sesiones
             routing / aislamiento / despliegue
                           │
                 ┌─────────┴─────────┐
                 │                   │
              YouTube              TikTok
              perfiles             perfiles
```

La herramienta será independiente, pero el EEC podrá visualizarla y administrarla como parte del control operativo global.

## 4. Regla fundamental de routing

Cada contenido debe tener un destino explícito y verificable.

Ejemplos:

- `video_001 → TikTok_1` → únicamente perfil/sesión de TikTok 1.
- `video_002 → TikTok_5` → únicamente perfil/sesión de TikTok 5.
- `video_003 → YouTube_Canal_12` → únicamente perfil/sesión del canal 12.

La herramienta debe impedir que el sistema dependa de memoria humana, ventanas abiertas al azar o selección manual ambigua de cuentas.

## 5. Relación con YOUTUBE-AUTOMATION

YOUTUBE-AUTOMATION continúa siendo la fábrica/orquestador audiovisual: decide y ejecuta el flujo de producción audiovisual.

Browser Factory se encarga de la capa operacional de cuentas y destinos:

- identificar el destino;
- resolver el perfil correcto;
- mantener aislamiento de sesiones;
- asociar cuenta/perfil/proxy cuando corresponda;
- abrir o conectar la sesión adecuada;
- ejecutar el despliegue/publicación solicitado;
- devolver estado, resultado y evidencia al flujo superior.

La meta es que YOUTUBE-AUTOMATION pueda trabajar con una abstracción como `publicar(destino, contenido)` sin tener que administrar manualmente ventanas y sesiones.

## 6. Relación con EEC

El EEC será el plano visual/control operativo donde pueda verse y administrarse la Browser Factory, pero la herramienta conservará su propio runtime, contratos y lógica.

Esto permite que el EEC sea interfaz/control plane sin convertir la Browser Factory en una dependencia estructural del panel.

## 7. Relación con HANDS

Cuando una operación necesite interacción física/desktop, Browser Factory podrá utilizar HANDS como capa de ejecución.

Conceptualmente:

```text
YOUTUBE-AUTOMATION
        ↓
Browser Factory
        ↓
Perfil / sesión / destino
        ↓
HANDS (cuando sea necesario)
        ↓
Browser / plataforma
```

Browser Factory y HANDS son responsabilidades diferentes.

## 8. Escalabilidad objetivo

El diseño debe contemplar una progresión aproximada:

- 1–5 cuentas: operación sencilla.
- 10–50 cuentas: gestión centralizada.
- 100–200+ cuentas: operación de fábrica, sin depender de manipulación manual de cada ventana.

La escala objetivo es una razón central para crear la herramienta.

## 9. Ingeniería inversa de Dolphin Anty

Dolphin Anty se utilizará como referencia funcional para estudiar, entre otros aspectos:

- perfiles de navegador;
- aislamiento de sesiones;
- gestión de múltiples cuentas;
- proxies y asociación por perfil;
- automatización;
- APIs;
- integración con DevTools/MCP;
- organización visual;
- operaciones masivas;
- sincronización y administración de perfiles;
- límites y modelo de seguridad.

La ingeniería inversa busca reconstruir capacidades y arquitectura funcional útil para ALBRA, no copiar literalmente código propietario ni convertir Dolphin en una dependencia de ejecución.

## 10. Mejoras ALBRA previstas conceptualmente

La diferencia principal frente a una herramienta genérica de perfiles es que Browser Factory conocerá el concepto de **destino de contenido**.

La herramienta debe evolucionar desde:

`"Tengo muchas ventanas/cuentas"`

a:

`"Tengo una fábrica de destinos y cada contenido sabe exactamente a qué cuenta pertenece"`.

Esto permitirá posteriormente coordinar múltiples canales y cuentas de forma sistemática con YOUTUBE-AUTOMATION.

## 11. Estado

- Decisión arquitectónica: APROBADA.
- Clasificación: TOOL / SYSTEM, no AGENT.
- Ingeniería inversa: EN CURSO.
- Implementación: NO DECLARADA.
- Integración con YOUTUBE-AUTOMATION: NO DECLARADA.
- Integración visual con EEC: DISEÑO CONCEPTUAL.

Ninguna fase debe declararse terminada sin evidencia verificable.

## 12. Fuente de verdad

La implementación técnica futura tendrá su propio repositorio. `ALBRA8/CONTEXT` conserva aquí únicamente la decisión, arquitectura conceptual, relaciones, reglas y estado general del proyecto.

## 13. Decisión relacionada

Ver `02_DECISIONS/DECISION_LOG.md` → `D-0016 — ALBRA Browser Factory como herramienta independiente`.


## 14. Actualización de estado — 2026-10-19

**Corrección aportada por el propietario:** Browser Factory ya está implementada en su PC. El estado anterior “Implementación: NO DECLARADA” solo reflejaba la evidencia recuperada por CONTEXT y ya no debe interpretarse como que el producto no existe.

- Estado actual de conocimiento: **IMPLEMENTACIÓN LOCAL REPORTADA POR EL PROPIETARIO; NO INSPECCIONADA INDEPENDIENTEMENTE**.
- No se ha inspeccionado el árbol local, código, manifest, hash, tests, instalador ni conexión de la instancia del PC con los repositorios disponibles. No declarar todavía qué componentes o integraciones están implementados.
- El nombre del producto es **ALBRA Browser Factory**. Dolphin Anty aparece únicamente como referencia funcional para ingeniería inversa, no como nombre del producto ALBRA.
- Próxima auditoría cuando el código local esté accesible: inventario de archivos/versiones, arquitectura, aislamiento de perfiles y sesiones, routing explícito contenido→destino, almacenamiento/secretos, APIs/IPC, launcher, pruebas, permisos y conexión con YOUTUBE-AUTOMATION/HANDS/EEC.

Esta actualización registra la declaración del propietario sin fingir acceso al PC ni elevar una afirmación a verificación técnica.
