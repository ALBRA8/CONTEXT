# ARQUITECTURA REAL DEL ECOSISTEMA ALBRA — ORDEN MAESTRA

Fecha: 2026-10-24 · Estado: implementación F1–F5 verificada (sin Docker en sandbox)

## 1. Arquitectura de despliegue (implementada)

```
COMPUTADOR / VPS
  └─ INSTALADOR ALBRA (Next.js + Prisma/SQLite)         repo: ALBRA8/INSTALADOR
       ├─ detecta OS/arquitectura, Docker Engine + Compose v2 (fail-closed)
       ├─ despliega el PANEL (producto contenedorizado del catálogo)
       │    compose albra: publica solo el puerto del operador (3900→3000)
       │    inyecta ALBRA_OPS_URL + ALBRA_OPS_TOKEN (SecretStore de plataforma)
       └─ red Docker administrada: albra-agents (externa, compartida)
  └─ PANEL DE CONTROL (Next.js + Prisma/SQLite)          repo: ALBRA8/PANEL-DE-CONTROL
       ├─ vista "Agentes ALBRA": catálogo + estados REALES
       ├─ NO ejecuta Docker: delega operaciones al INSTALADOR
       └─ contrato EEC ops (Bearer + requestId + nonce + expiración ≤5 min)
  └─ AGENTES (p. ej. CHISMOSO)                            repo: ALBRA8/AGENTE-CHISMOSO
       └─ compose.albra.yaml: red interna privada + albra-agents,
          sin puertos al host, healthchecks reales, volúmenes persistentes
```

## 2. Contrato EEC de operaciones (único, versionado, documentado)

- **Versión**: `1.0.0` (`EEC_OPS_CONTRACT_VERSION`).
- **Endpoint único**: `POST /api/ops` en el INSTALADOR (`GET` describe el contrato).
- **Autenticación**: `Authorization: Bearer <ops-token>`; token cifrado en el
  SecretStore (`eec.ops.token`), provisionado una vez vía `POST /api/ops/token {confirm:true}`.
- **Solicitud**: `{ contractVersion, requestId (uuid), operation, productId, params?, nonce (uuid), issuedAt, expiresAt }` — expiración ≤ 5 min, nonce anti-replay en memoria.
- **Respuesta**: `{ contractVersion, requestId, accepted, operation, productId, runId? | state? | health? | detail?, error?{code,message} }`.
- **Operaciones**: INSTALL (async→runId), START, STOP, RESTART, HEALTH, UPDATE, ROLLBACK, BACKUP, RESTORE, UNINSTALL (params: dataMode KEEP|EXPORT|DELETE + purgeData), DIAGNOSTICS, STATUS (sync).
- **Estados asíncronos**: el PANEL consulta `GET /api/runs/[runId]` (proxy `GET /api/v1/managed/runs/[runId]`) — pasos + unidades por módulo reales.
- **Auditoría**: OPS_EXECUTED / OPS_REJECTED en el INSTALADOR; ManagedOperation (SQLite PANEL) registra cada delegación con resultado real.

## 3. Despliegue contenedorizado (motor del INSTALADOR)

Flujo real (`core/container-deploy.ts` + rama contenedorizada de `installer.ts`):

1. PREFLIGHT: Docker Engine + Compose v2 (fail-closed), OS/permisos.
2. Red `albra-agents` (externa, compartida; idempotente).
3. Directorio gestionado + compose del paquete.
4. Secretos: generados (random 32B) + env estático + secretos de plataforma → `.env.albra` modo 0600 con O_EXCL (nunca rota secretos en silencio).
5. **Fijado de imágenes**: `docker pull` + `image inspect .RepoDigests` → digest SHA-256 REAL; sin digest no hay despliegue.
6. Compose materializado con digests; validación del descriptor estricto (zod) antes de tocar Docker.
7. `compose pull` → `compose up -d` → espera activa de salud (healthchecks reales de Compose, timeout configurable, fail-fast ante unhealthy).
8. Registro persistente en Prisma (`ContainerDeployment`) + auditoría.
9. START/STOP/RESTART/STATUS/HEALTH → `core/container-lifecycle.ts` (nunca `down`, nunca borra volúmenes).
10. UNINSTALL: `compose down --remove-orphans` SIN `-v`; volúmenes solo con `purgeData=true` + `dataMode=DELETE` (doble confirmación PANEL→INSTALADOR).

## 4. Productos contenedorizados del catálogo (bóveda .albra)

| producto | versión | despliegue | servicios | secretos generados |
|---|---|---|---|---|
| panel-de-control | 1.0.0 | compose publicado en :3900 | panel | EEC_SESSION_SECRET |
| chismoso | 1.5.0 | compose red interna (sin host) | app+scheduler+backup+redis | CHISMOSO_JWT_SECRET |

Variables reales de CHISMOSO declaradas desde su código (no inventadas):
`CHISMOSO_JWT_SECRET` (generada), `CHISMOSO_AUTH_TOKENS` (hashes del operador vía
`chismoso auth issue`), `CHISMOSO_MESH_SECRETS` (solo mesh). Documentadas en
`AGENTE-CHISMOSO/albra/.env.albra.example`.

## 5. Estado de implementación (clasificación)

**IMPLEMENTADO Y VERIFICADO (tests ejecutados)**
- Motor de despliegue Docker Compose + pin de digests + descriptor estricto (28/28 tests INSTALADOR; runner falso — sin Docker en sandbox).
- Despacho EEC de START/STOP/RESTART/HEALTH/STATUS/UNINSTALL a contenedores.
- Puente PANEL→INSTALADOR (contrato EEC completo) + vista Agentes ALBRA (123/123 tests PANEL, tsc, lint, build).
- Descriptor y contrato de CHISMOSO (deployment.json + .env.albra.example) — YAML/JSON validados.
- E2E del puente sin Docker: PANEL→INSTALADOR STATUS/INSTALL (accepted + runId real), rechazos honestos (401, contrato, PRODUCT_NOT_INSTALLED), auditoría ManagedOperation.
- Fix por E2E: clave de servicio con formato conforme a la puerta de acceso.

**IMPLEMENTADO, PENDIENTE DE PRUEBA (requiere Docker)**
- Despliegue real del PANEL y CHISMOSO con `compose up` + healthchecks.
- Persistencia tras reinicio de contenedores; backup/restore con volúmenes.
- Publicación de imágenes en GHCR y fijado de digests reales.

**PENDIENTE DE IMPLEMENTACIÓN**
- Instalación automatizada de Docker en hosts sin él (actualmente fail-closed con mensaje; método oficial get.docker.com pendiente de conectar).
- Windows container adapter (Docker Desktop) — descriptor listo, ejecución sin probar.

**BLOQUEADO POR INFRAESTRUCTURA**
- Este sandbox no tiene Docker daemon ni registro OCI: toda prueba E2E con contenedores queda documentada como pendiente.

## 6. Pasos exactos en un equipo limpio (VPS Linux)

1. `git clone https://github.com/ALBRA8/INSTALADOR && cd INSTALADOR`
2. Instalar Bun (`curl -fsSL https://bun.sh/install | bash`) + `bun install`
3. Instalar Docker si falta: `curl -fsSL https://get.docker.com | sh` (oficial)
4. `bun run db:push && bun run dev` → INSTALADOR en :3000
5. Provisionar token ops: `POST /api/ops/token {"confirm":true}` → guardar
6. Instalar el PANEL desde la GUI (catálogo → ALBRA Panel de Control → Instalar + clave de acceso demo) — despliega el contenedor y lo registra
7. Abrir `http://<host>:3900` → login operador → vista "Agentes ALBRA"
8. Preflight + Instalar CHISMOSO desde el PANEL → seguir el run → verificar salud
9. STOP/START desde el PANEL: los volúmenes persisten (SQLite/reportes/backups)
