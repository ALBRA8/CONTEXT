# AGENTE-LEADS — Auditoría Radar de Clientes (2026-10-13)

- Estado: VERIFIED (auditoría de solo lectura ejecutada sobre HEAD real)
- Repositorio auditado: `ALBRA8/AGENTE-LEADS` @ `e73d15b` (main, árbol limpio)
- Método: 4 frentes de auditoría en paralelo + verificación reproducible (typecheck, tests, build, lint). Evidencia por `ruta:línea`.
- Autor de la auditoría: Constructor principal (Super Z) con autorización del propietario registrada en D-0015.

## 1. Verificación reproducible ejecutada

| Comando | Resultado |
|---|---|
| `npm run typecheck` (raíz bot) | PASS, 0 errores |
| `npm test` (raíz bot) | 219 pass / 0 fail / 1 skip (LLM real sin credenciales) |
| `npx tsc --noEmit` (dashboard, con dependencias instaladas) | FAIL: 22 errores, todos en `src/lib/leads/overpass-adapter.ts` |
| `npm run build` (dashboard) | PASS solo porque `next.config.ts` tiene `typescript.ignoreBuildErrors: true` |
| `npm run lint` (dashboard) | ROTO: ESLint 9 sin `eslint.config.js` (config legacy), exit 2 |
| Tests dashboard | Inexistentes (0 tests) |

## 2. Hallazgos P0 (confirmados)

1. **`/api/seed-demo` sin auth**: crea usuario demo con `role:"admin"` y devuelve la contraseña en la respuesta (`dashboard/src/app/api/seed-demo/route.ts:7,73-76`; `dashboard/src/lib/auth.ts:69-81`). Acceso admin público en instancias expuestas.
2. **Score comercial constante**: `scoreLead` invocado con `validation:{}`, `evidence:[]`, `research_state:"VALIDATED"` en `dashboard/src/app/api/search/route.ts:313-325` y `chat/route.ts:185`; `scoring.ts` solo lee esos 3 campos → **todos los leads reciben score 15**. Los 8 parámetros comerciales son código muerto. Breakdown descartado (no persistido).
3. **Contacto truncado a 1 carácter**: `web-search-adapter.ts:160-161` guarda `phoneMatch?.[0]` / `emailMatch?.[0]` (primer carácter del match: "+", "c").
4. **22 errores de TypeScript ocultos** por `ignoreBuildErrors: true` (`overpass-adapter.ts`).
5. **SSRF en dashboard**: `freshness-checker.ts` hace `fetch` HEAD con `redirect:"follow"` y `tls.connect({rejectUnauthorized:false})` a URLs editables por el usuario, sin aplicar ningún guard SSRF (el bot raíz sí tiene `src/agent/core/ssrf_guard.ts`; 0 usos en dashboard).
6. **Escalada de privilegios en signup**: todo registro recibe `role:"admin"` (`dashboard/src/app/api/auth/signup/route.ts:31-37`).

## 3. Hallazgos P1 principales (fase siguiente)

- Google Places: FieldMask sin `place_id/coordenadas/rating/userRatingCount/businessStatus`; sin paginación `nextPageToken` (máx 20, truncado silencioso); sin timeout; keywords ignoradas (hasta 25 llamadas de pago idénticas).
- Sin fallback entre proveedores ni envelope de error (`fallo` se presenta como `0 resultados`); dedup débil `name|city` (el bot raíz tiene dedup multi-señal EXACT/STRONG/PROBABLE reutilizable).
- `qualification.ts` viola "no encontrado ≠ no tiene" (TIER_F por `website=null`).
- Mass-assignment en PATCH (`prospects`, `offers`); notas cross-tenant (`notes/route.ts:30-31`); sin rate-limit; secretos en claro en DB (`Integration`); sin middleware de auth (500≠401); Caddy sin TLS.
- Clasificación de presencia digital: no existe ningún estado.
- Ficha de negocio usa `GET /api/prospects?pageSize=9999` y filtra en cliente.

## 4. Pendientes P2/P3 confirmadas (0% en repo)

- PageSpeed Insights / Lighthouse: 0 referencias. Gemini/URL context: 0 referencias.
- Exportaciones CSV/PDF: 0% (sin botones, rutas ni librerías).
- Borradores por canal (llamada/WhatsApp/email): 0% (solo propuesta comercial genérica).
- Radar de Nichos dedicado: 0%. Kanban: 0%.

## 5. Capacidades preservables verificadas (no reconstruir)

- Bot raíz: pipeline determinístico, evidence engine (NOT_FOUND ≠ CONFIRMED_ABSENT), dedup multi-señal, scoring con pesos+breakdown, fallback de proveedores con taxonomía de errores, 219 tests verdes.
- Dashboard: auth de páginas, aislamiento de lecturas por `userId`, tech-stack-detector y freshness-checker sólidos, CRM con filtros/estados/historial, UI en español con shadcn/ui.
