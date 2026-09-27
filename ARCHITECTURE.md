# Arquitectura — DeskcommCRM

> Visión de 1 página. La profundidad vive en `docs/specs/` y `docs/stories/epics/MASTER.md`.
> Mapa de toda la documentación: [`docs/index.md`](docs/index.md).
> Estado real de implementación (qué está listo vs. incompleto): [`docs/current-state.md`](docs/current-state.md).

## Capas

- **App (Next.js 16 App Router)**: UI + Route Handlers en el mismo repo. Server Components por default, Client donde hace falta estado. Middleware de borde en `proxy.ts` (Next 16 renombró `middleware.ts` → `proxy.ts`).
- **DB (Supabase Postgres)**: RLS en toda tabla tenant-aware vía `fn_user_org_ids()`. Migrations versionadas en `supabase/migrations/`.
- **Auth (Supabase Auth + `@supabase/ssr`)**: cookie SameSite=Strict. Siempre `getUser()` en el server, nunca `getSession()`. **MFA TOTP es opcional y lo activa quien administra** — dos políticas independientes que se suman (`platform_admins.mfa_required` y `organizations.settings.security.mfa_required`), ambas con default **no exigir**; regla pura en `lib/auth/politica-mfa.ts`. Esta línea decía "forzado para admin/super-admin", que era la regla antigua: como `install.sh` crea al dueño como platform admin, toda instalación self-host recibía un bloqueo de pantalla completa justo después del onboarding. Registrar y probar son cosas distintas — quien TIENE un factor lo prueba en la sesión siempre, independientemente de la política.
- **Realtime (Supabase Realtime)**: `postgres_changes` para inbox/kanban; `broadcast` para señales ligeras.
- **Storage (Supabase Storage)**: bucket `whatsapp-media` privado, URLs firmadas.
- **WhatsApp (WAHA Plus / engine NOWEB)**: webhooks con HMAC-SHA512; throttle anti-baneo; detección de STOP.
- **Colas (event sourcing ligero)**: tabla `event_log` + workers vía cron. Un trigger de Postgres NUNCA hace HTTP.
- **Rate limit (Upstash Redis)**: contador de **ventana fija** (`INCR` + `EXPIRE`) en `lib/ai/dispatcher/rate-limit.ts`, con fallback en memoria cuando falta Redis. ⚠️ Aplicado hoy en solo 2 puntos (webhook de captación y dispatcher de IA) — la superficie pública de auth no lo tiene. Ver [`docs/threat-model.md`](docs/threat-model.md) §T1.
- **AI (Vercel AI Gateway)**: Anthropic primario, OpenAI de respaldo para embeddings.
- **Observability (Sentry)**: `beforeSend` elimina PII (CPF/email/teléfono) y headers sensibles.

## Multi-tenancy

`organization_id uuid not null` en toda tabla tenant-aware. RLS vía helper. El service role se salta la RLS — los handlers admin **DEBEN** filtrar `organization_id` manualmente, resuelto de una fuente confiable (cookie/JWT/webhook secret/path token), nunca del body.

Detalles: [`docs/specs/01-spec-platform-base.md`](docs/specs/01-spec-platform-base.md).

## API REST `/api/v1/`

- JSON snake_case. UUID v4. ISO-8601 UTC. Dinero en `_cents` + `currency`.
- Wrappers `ok()` / `fail()` en `lib/api/wrappers.ts`.
- Auth dual: cookie de sesión (frontend) o `Authorization: Bearer tok_...` (server-to-server).
- `X-Request-Id` en toda response, inyectado en `proxy.ts` y correlacionado con el audit log.
- `Idempotency-Key` es el contrato previsto para los POST de creación; **hoy implementado en 1 ruta** (`lgpd/requests/[id]/approve`). Ver [`docs/current-state.md`](docs/current-state.md) §4.
- Detalles: [`docs/specs/01-spec-platform-base.md`](docs/specs/01-spec-platform-base.md) §API.

## Flujo de una solicitud

**Ruta autenticada de tenant** (`/api/v1/*`, 166 handlers):

```
request → proxy.ts (X-Request-Id, x-pathname; ¿isPublicPath? → bypass;
                    si no, valida la sesión de Supabase vía la cookie sb-deskcomm-auth)
        → route handler:
             1. Zod valida el input externo
             2. guard: requireRole() | requirePlatformAdmin() | secret/HMAC
             3. resolveActiveOrg() → organization_id de fuente confiable (nunca del body)
             4. query (RLS con el client de sesión, o filtro manual de org con service role)
             5. audit() fire-and-forget si hubo mutación
             6. ok(data, meta) | fail(code, message, status)
```

**Superficies sin cookie:** `/api/v1/cron/*` (Bearer `INTERNAL_CRON_SECRET`, fail-closed),
`/api/internal/*` (`x-internal-secret`), `/api/mcp` (Bearer `tok_...` contra `api_tokens`),
`/api/v1/webhooks/*` (HMAC + path token). Inventario completo en
[`docs/threat-model.md`](docs/threat-model.md) §1.

**Turno del agente de IA:** inbound de WhatsApp → HMAC + idempotencia → `event_log` →
worker → `runAgentTurn` (RAG + tools MCP) → guardrails antes de enviar → adapter WAHA →
handoff a humano si hay disparador. Diagrama: [`docs/architecture/agent-turn.html`](docs/architecture/agent-turn.html).

## Event log + workers

Los triggers de Postgres emiten filas en `event_log`. Los workers (cron / listener de Realtime) las consumen y disparan los side effects. Idempotencia vía `unique (organization_id, external_id)` + captura de `code === '23505'`.

Los workers viven en `workers/` (`ai-response`, `ai-sentiment`, `rag-indexer`, `media-persist`,
`media-derive`, `lgpd-export`, `lgpd-redact`, `storage-cleanup`, `agent-worker`), drenados
por los 10 endpoints de `app/api/v1/cron/`. Contrato: [`docs/specs/07-spec-events-workers.md`](docs/specs/07-spec-events-workers.md).

## Integraciones externas

| Servicio | Uso | Dónde | Si falta ⇒ |
|---|---|---|---|
| **Supabase** | Postgres + Auth + Realtime + Storage | `lib/supabase/{browser,server,admin}.ts` | la app no arranca (obligatorio siempre) |
| **WAHA Plus** (NOWEB) | WhatsApp: envío, recepción, sesiones multinúmero | `lib/waha/` | canal no disponible; obligatorio en producción |
| **Upstash Redis** | rate limit + debounce de RAG | `lib/ai/dispatcher/rate-limit.ts`, `lib/ai/rag/debounce.ts` | se degrada a memoria con `warn` |
| **Vercel AI Gateway** | LLM + embeddings (`@ai-sdk/anthropic\|openai\|google`) | `lib/ai/` | el agente no responde |
| **Nuvemshop** | e-commerce: pedidos, productos, webhooks LGPD | `lib/nuvemshop/` | opcional (`NUVEMSHOP_ENABLED`) |
| **Sentry** | errores + performance, `beforeSend` sanitiza PII | `sentry.*.config.ts`, `instrumentation*.ts` | opcional |
| **Resend** | correo transaccional (invitación al equipo) | `lib/email/` | opcional — la invitación cae en copiar al portapapeles |
| **MCP** | CRM expuesto como tools para agentes | `app/api/mcp/`, `lib/mcp/` | — |

## Hardening

- Error boundaries en `app/error.tsx`, `app/app/error.tsx`, `app/(public)/error.tsx`, `app/global-error.tsx` (captura en Sentry + eventId visible).
- Páginas personalizadas 404/403/500/503 con textos canónicos (PT-BR por default, con traducción al español).
- Loading skeletons en rutas P0.
- E2E con Playwright + axe-core.
- Detalles: [`docs/stories/epics/EPIC-12-hardening.md`](docs/stories/epics/EPIC-12-hardening.md).

## Dónde profundizar

- [`docs/prd/`](docs/prd/) — PRDs (visión, alcance del MVP, KPIs, plataforma base, customer 360, WhatsApp, pipeline, IA-RAG, Nuvemshop).
- [`docs/specs/`](docs/specs/) — specs técnicas con schema SQL y payloads.
- [`docs/business-rules/`](docs/business-rules/) — reglas de negocio fuera del código.
- [`docs/stories/epics/MASTER.md`](docs/stories/epics/MASTER.md) — plan de ejecución por epic/wave.
- [`CLAUDE.md`](CLAUDE.md) — convenciones no negociables (multi-tenancy, idempotencia, RBAC, LGPD, WAHA, anti-patterns).
- [`AGENTS.md`](AGENTS.md) — contrato portable para agentes de código (cualquier herramienta).
- [`docs/index.md`](docs/index.md) — índice de toda la documentación.
- [`docs/harness-audit.md`](docs/harness-audit.md) — madurez del harness y huecos de verificación.
- [`docs/threat-model.md`](docs/threat-model.md) — superficie de ataque del self-host.
