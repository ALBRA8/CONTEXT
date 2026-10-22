# AGENTE-KREA — auditoría de estado actual

- Fecha: 2026-10-21
- Repositorio fuente: `ALBRA8/AGENTE-KREA`
- Rama consultada: `main`
- HEAD visible: `460605e5b4966e5e9243c1ec3fcb4852c6758e1b` (2026-10-08)
- Alcance: inspección documental de GitHub; no se ejecutó el sistema ni su suite en un entorno limpio.
- Escritura: únicamente en `ALBRA8/CONTEXT`; no se modificó AGENTE-KREA.

## 1. Dictamen ejecutivo

KREA V2.1 ya no debe describirse solamente como una fábrica de libros o un generador de productos digitales. El commit principal V2.1 presenta una **Product Factory comercial** con flujo de oportunidad → fit/evaluación → decisión → dossier → arquitectura y especificación → blueprint/packaging/oferta → ejecución de fábrica → activos/versiones/QA → readiness comercial → paquete de lanzamiento → contenido → feedback/validación. Mantiene además un sistema general de agente, memoria, skills, trazas, auditoría y MCP.

El estado documental del último commit es **CONDITIONALLY APPROVED**, no aprobación incondicional ni certificación de producción: el propio mensaje del commit indica 241 tests y Golden Flows aprobados, pero proveedores externos `NOT_VERIFIED`.

## 2. Evolución y evidencia del repositorio

### 2.1 Secuencia relevante de commits

- `3f177015...` — KREA V2 Hardening: autenticación real, Product Brain, Book Factory, PDF Factory, Visual QA, handoff; 162 tests reportados, 42 rutas API y 13 modelos Prisma.
- `f56c2f85...` — se retiraron tests solo documentales y se mantuvo la suite Vitest real.
- `1990bdbb...` — V2.1 Commercial Product Engine: blueprint, packaging, offer architecture, factory orchestrator, asset manager, versioning, product QA, commercial readiness, launch package, content engine y commercial feedback; MCP fix; 241 tests, 56 rutas API y 20 modelos Prisma reportados.
- `396c2c8d...` — clean-room: rutas portables, `.gitignore`, eliminación de artefactos de runtime rastreados y setup de tests.
- `bb83dd27...` — arreglos de contratos de esquemas, imports de product factory, ruta portable de `.env` y setup de tests.
- `460605e5...` — verificación final V2.1: “CONDITIONALLY APPROVED”; Golden Flows y 241 tests reportados; proveedores externos `NOT_VERIFIED`.

Las cantidades de tests, rutas y modelos son afirmaciones de commits del proyecto. No fueron reejecutadas de manera independiente durante esta auditoría.

## 3. Arquitectura observada

### 3.1 Núcleo de agente y aprendizaje

El esquema Prisma contiene persistencia para:
- `Memory`: memoria con dominio, tipo, fuente, evidencia, confianza, `truthLevel`, relevancia, utilidad, decaimiento, scope, estado y `lastVerified`.
- `Skill`: procedimientos, herramientas requeridas, verificación, pitfalls, evidencia, confianza, tasa de éxito, versión y estados PROPOSED/VALIDATING/ACTIVE/DEPRECATED/RETIRED.
- `Execution`: herramientas/skills usados, inputs/outputs, errores, evidencia, aprobaciones, coste, estado, tiempos y correlation ID.
- `Feedback`: feedback vinculado opcionalmente a una ejecución, análisis, aprendizaje, evidencia y estado de validación.
- `AgentInteraction`: comunicación agente-a-agente con objetivo, restricciones, salida solicitada, estado, resultado, evidencia y correlación.
- `AuditEvent`: acción, recurso, resultado, evidencia y datos de auditoría.
- `Session`: token de sesión, usuario y expiración.

Esto evidencia **modelos de persistencia previstos en el esquema**; no demuestra por sí solo que cada ciclo de aprendizaje, skill o comunicación entre agentes esté operativo de extremo a extremo.

### 3.2 Product Factory comercial

El esquema también contiene entidades específicas del ciclo de vida comercial:
- `ProductOpportunity`: problema, audiencia, dominio, alternativas, estado, fit score y decisión.
- `ProductDossier`: estado de ciclo de vida, arquitectura, especificación, economía, handoff, artefactos, timeline y relación de versiones.
- `Handoff`: destino/proveedor, objetivo, producto/versión, especificación, restricciones, criterios de aceptación, dependencias, evidencia, riesgos, autonomía, aprobaciones, estado, intentos y estado de envío externo.
- `CommercialProduct`: propuesta de valor, cliente objetivo, diferenciación, formato, activos incluidos/excluidos, precio, monetización, distribución, pruebas requeridas, supuestos y riesgos.
- Además, relaciones/modelos para blueprint, assets, factory executions, version records, launch packages y commercial feedback.

La arquitectura ya supera una simple “fábrica de ebooks”: está modelada para gestionar productos digitales/comerciales y su ciclo de vida. La disponibilidad real de cada fábrica/proveedor debe seguir comprobándose por separado.

### 3.3 Stack declarado

En `package.json`: Next.js 16, React 19, TypeScript, Prisma 6 con SQLite, Zod, Vitest, ESLint y SDK `z-ai-web-dev-sdk`. El paquete figura como `private`, versión `0.2.0`; esto es la versión del package, no una declaración equivalente a KREA V2.1 del producto.

## 4. Hallazgos y riesgos que merecen atención

### KREA-A1 — E2E no ejecutable mediante el script npm actual

En el `package.json` actual:
`"test:e2e": "echo 'E2E: Run manually with dev server' && exit 0"`

Este script siempre devuelve éxito sin ejecutar E2E. Por tanto, un pipeline que se limite a `npm run test:e2e` podría registrar PASS sin haber ejecutado pruebas. No prueba que los Golden Flows unitarios reportados sean falsos; sí significa que **ese comando no certifica E2E**. Requiere un runner real o que CI no lo presente como gate E2E.

### KREA-A2 — Comando de base de datos potencialmente destructivo

El script `db:push` usa `prisma db push --accept-data-loss`. Es una herramienta operativa de riesgo: puede aceptar cambios de esquema que impliquen pérdida de datos. No ejecutarlo automáticamente en entornos con datos ni tratarlo como migración segura. Preferir migraciones revisadas y backups, según el entorno.

### KREA-A3 — Doctor API sin autenticación visible en el handler revisado

`src/app/api/doctor/route.ts` expone un handler GET que ejecuta `KreaDoctor().runFullCheck()` y devuelve el reporte con 200/503 según estado. En ese handler no se ve control de sesión/autorización. No se concluye vulnerabilidad definitiva sin revisar middleware, despliegue y el contenido completo del reporte; queda como **revisión de exposición requerida** antes de producción.

### KREA-A4 — Verificación de fábricas físicas sigue siendo parcial

El propio `VERIFICATION-REPORT.md` histórico marca como NOT_VERIFIED la renderización PDF real y la inspección de QA visual sobre PDF real; Feedback→Learning y Failure→Recovery también tenían pruebas estructurales sin ejecución real. El reporte es histórico y sus comandos de test han evolucionado, así que no se debe usar como estado final único. La conclusión vigente del commit V2.1 sigue siendo condicional y proveedores externos NOT_VERIFIED.

### KREA-A5 — Integraciones externas y envío de handoff no certificados

El último commit de verificación dice expresamente que los proveedores externos son `NOT_VERIFIED`. El modelo Handoff conserva `externalSendStatus=NOT_VERIFIED` por defecto. No afirmar que KREA despacha de forma real a Codex/Antigravity u otros proveedores sin evidencia runtime específica.

### KREA-A6 — La presencia de modelos no equivale a comportamiento probado

Memory, Skill, AgentInteraction, Execution y Feedback están representados en Prisma. La auditoría de solo lectura no confirmó cada flujo a través de API, base de datos y UI ni verificó aislamiento, concurrencia y recuperación bajo carga en un entorno limpio.

## 5. Seguridad y limpieza del repositorio

Los commits de hardening describen sesiones reales, hashing de contraseñas, aislamiento por usuario, controles SSRF, validación/sanitización y límites de reparación. Los commits clean-room corrigieron rutas absolutas, el `.gitignore` y artefactos de runtime rastreados. Se considera una mejora significativa respecto al estado inicial.

Límites:
- Esta pasada no repitió tests de seguridad ni probó rutas con HTTP real.
- No se inspeccionaron secretos de entorno ni proveedores configurados localmente.
- La existencia de controles unitarios no es una auditoría de penetración ni un aval de producción.

## 6. Rol correcto en ALBRA

KREA debe conservar su especialización: **Product Factory / construcción y ciclo de vida de productos comerciales digitales**. No debe absorber la investigación jurídica ni convertirse en un agente monolítico que haga todo.

Fronteras recomendadas:
- **KREA:** transforma oportunidades y evidencia en decisiones de producto, arquitectura, especificación, producción, QA, packaging, lanzamiento y aprendizaje comercial.
- **LEADS:** adquisición/calificación de oportunidades comerciales y radar de clientes, según sus contratos.
- **CHISMOSO:** investigación/reconocimiento especializado según su contrato actual; no atribuirle responsabilidades legales sin auditoría.
- **Despacho Jurídico/pool legal:** análisis de derechos, licencias, cumplimiento y clearance cuando corresponda.
- **NEX-SCOPE:** inteligencia de nichos/YouTube y plataforma agentic de análisis; puede suministrar señales de oportunidad, pero no debe asumirse una integración automática.
- **YOUTUBE-AUTOMATION:** orquestación audiovisual, no propietario de la fábrica comercial genérica.

Estos límites son una recomendación arquitectónica de ALBRA, no evidencia de integraciones ya conectadas entre repositorios.

## 7. Estado de aceptación

| Área | Estado en esta auditoría |
|---|---|
| Evolución V2.1 y arquitectura declarada | VERIFICADO DOCUMENTALMENTE |
| 241 tests / Golden Flows | REPORTADO POR COMMIT; NO REPETIDO |
| E2E vía `npm run test:e2e` | NO ES UN RUNNER; TERMINA EN CERO |
| PDF real y QA visual sobre archivos reales | NO CERTIFICADO EN ESTA PASADA |
| Proveedores externos / handoff real | NOT_VERIFIED por el propio commit |
| Seguridad integral / producción | NO CERTIFICADA |
| Cambios en código de KREA | NINGUNO |
| Cambios en CONTEXT | DOCUMENTACIÓN SOLAMENTE |

## 8. Próximos gates sugeridos

1. Ejecutar clean-room real: install reproducible, Prisma generate/migrate, TypeScript, lint, Vitest y build.
2. Sustituir o excluir el falso script E2E; correr Golden Flows HTTP reales contra DB temporal.
3. Probar autorización de `/api/doctor` y minimizar/redactar detalles operativos expuestos.
4. Probar PDF real → inspección visual/QA → reparación acotada → artefacto final, conservando evidencia.
5. Verificar cada proveedor/handoff en entorno de prueba, incluyendo fallo, timeout, retry, idempotencia y aprobación humana.
6. Revisar backup/migraciones y eliminar el uso automático de `--accept-data-loss` en cualquier flujo productivo.
7. Mantener KREA condicionalmente aprobado hasta que los gates aplicables tengan evidencia reproducible.

## 9. Fuentes directas consultadas

- `package.json` — scripts, stack y dependencias.
- `prisma/schema.prisma` — modelos de usuario, memoria, skills, ejecuciones, feedback, interacción, auditoría, oportunidades, dossiers, handoff y producto comercial.
- `src/app/api/doctor/route.ts` — handler de diagnóstico.
- `vitest.config.ts` — configuración Vitest.
- `VERIFICATION-REPORT.md` — reporte histórico de verificación con límites explícitos.
- Historial de commits `3f177015...`, `f56c2f85...`, `1990bdbb...`, `396c2c8d...`, `bb83dd27...`, `460605e5...`.

Fuentes:
- https://github.com/ALBRA8/AGENTE-KREA/commits/main
- https://github.com/ALBRA8/AGENTE-KREA/blob/main/package.json
- https://github.com/ALBRA8/AGENTE-KREA/blob/main/prisma/schema.prisma
- https://github.com/ALBRA8/AGENTE-KREA/blob/main/src/app/api/doctor/route.ts
- https://github.com/ALBRA8/AGENTE-KREA/blob/main/vitest.config.ts
- https://github.com/ALBRA8/AGENTE-KREA/blob/main/VERIFICATION-REPORT.md

## 10. Conclusión

KREA V2.1 es una base de Product Factory madura y mucho más amplia que una fábrica de libros. La aprobación sigue siendo **CONDICIONAL**: la documentación acredita diseño, evolución y tests reportados, pero no certifica por sí sola producción, proveedores externos, PDF/QA físico ni E2E real. Preservar la especialización y cerrar los gates con pruebas reproducibles antes de declararlo listo para producción.
