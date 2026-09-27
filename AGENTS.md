# AGENTS.md — DeskcommCRM

> Contrato para **cualquier** agente de código (Codex, Cursor, Copilot, Amp, Claude Code).
> Este archivo es el núcleo portable. La **doctrina completa y no negociable vive en
> [`CLAUDE.md`](CLAUDE.md)** — léela antes de tocar código. Aquí está lo mínimo
> para no causar daño.

---

## Objetivo del proyecto

Sistema operativo de ventas open source con agentes de IA nativos, multinicho,
WhatsApp como canal primario (vía WAHA). Multi-tenant con RLS desde el día 1, LGPD
nativa. Monetización = self-host en VPS, no suscripción. Posicionamiento: [`VISION.md`](VISION.md).

**Consecuencia que cambia cómo trabajas:** el producto se distribuye como código.
Quien lo instala en un VPS **es** el usuario. Un cambio que funciona en la máquina del dev y
se rompe en el clon fresco es un bug de producto, no un detalle de entorno.

## Stack (CONFIRMADO en `package.json`)

Next.js 16 (App Router) · React 19 · TypeScript 6 estricto · Tailwind 4 ·
shadcn/ui · Supabase (Postgres + Auth + Realtime + Storage) · Upstash Redis ·
Vercel AI Gateway (`@ai-sdk/anthropic|openai|google`) · WAHA 2026.7.2 (engine NOWEB; sin bloqueo por tier) ·
Zod 4 · Vitest 4 · Playwright 1 · Sentry 10.

Solo la **major**, a propósito: es donde cambia el lenguaje, y es lo que
`tests/unit/agents-md-versoes.test.ts` verifica contra el `package.json`. Declarar la minor
aquí hacía que todo bump de Dependabot rompiera el `verify` (5 de los 8 paquetes) y no cubría nada
que la major no cubriera ya — issue #235. Para la versión exacta, `package.json` es la fuente.

Runtime: **Node ≥22** (`.nvmrc` = 22; los cuatro workflows fijan `node-version: 22` —
`ci` ×2, `perf`, `e2e`). Gestor: **pnpm 9.15.9** (`packageManager`).
Versión del producto: **no está escrita aquí, a propósito.** Esta línea afirmaba `1.0.0` hasta la
v1.6.0 — seis minors de retraso, y ningún test la vigilaba. Una afirmación de versión envejece en cada
release; un comando no. La que está publicada ahora:

```bash
git ls-remote --tags --refs origin 'refs/tags/v*' \
  | sed 's#.*refs/tags/v##' | awk '!/-/' | sort -V | tail -1   # awk, no grep -v -- '-':
                                                                # en una maquina con ugrep aquel no corre
```

El `package.json` **no** es la fuente de la versión del producto (sigue en `0.1.0`, y así es a
propósito). La fuente es el tag `v*` más la sección del `CHANGELOG.md` — que es pantalla de producto, leída
por el dueño del VPS. Cómo se decide el número: [`docs/doctrine/versionamento.md`](docs/doctrine/versionamento.md).

## Estructura que importa

| Path | Qué es |
|---|---|
| `app/api/v1/` | 166 route handlers REST (versionado por path) — 169 contando `app/api/**` |
| `app/api/internal/`, `app/api/mcp/`, `app/api/v1/cron/` | superficies sin cookie (secret/bearer propio) |
| `app/app/` | UI autenticada del tenant · `app/admin/` UI de plataforma |
| `app/actions/` | Server Actions (auth, onboarding, team, settings) |
| `lib/agent-engine/`, `lib/ai/` | runtime del agente, guardrails, RAG, dispatcher |
| `lib/api/wrappers.ts` | `ok()` / `fail()` — **úsalos siempre**, no armes el Response a mano |
| `lib/auth/require-role.ts` | `requireRole()` — guard canónico de RBAC |
| `lib/supabase/{browser,server,admin}.ts` | clients canónicos |
| `workers/` | workers de `event_log` + crons |
| `supabase/migrations/` | schema versionado · `supabase/baseline.sql` = lo que aplica el self-host |
| `proxy.ts` | middleware de Next 16 (auth de borde, `X-Request-Id`) |

## Comandos (CONFIRMADO en `package.json`)

```bash
pnpm install          # deps (frozen-lockfile en el CI)
pnpm dev              # dev server
pnpm build            # next build
pnpm lint             # eslint
pnpm typecheck        # tsc --noEmit (estricto)
pnpm test:unit        # vitest — EXCLUYE tests/invariants y tests/e2e
pnpm test:db          # invariantes de base de datos + gate del baseline (NECESITA Docker)
pnpm test:e2e         # Playwright (NECESITA la app corriendo + base sembrada)
pnpm gov:verify       # typecheck + lint + test:unit  ← verificación única actual
```

⚠️ **`pnpm gov:verify` NO lo cubre todo.** Omite `test:db` y `test:e2e`. Si tu
cambio toca schema, RLS o UI, un `gov:verify` en verde **no** es prueba — corre `pnpm test:db`
(exige Docker) y/o `pnpm test:e2e` tú mismo. Ver [`docs/harness-audit.md`](docs/harness-audit.md).

**Lo que cubre el CI.** `.github/workflows/ci.yml`: `verify` = typecheck + lint + test:unit;
`invariants` = `pnpm test:db` (aislamiento RLS + invariantes de gobernanza contra un Postgres
efímero pg15). `.github/workflows/perf.yml`: `build-and-size` = `pnpm build`.
`.github/workflows/e2e.yml` corre las specs de Playwright contra un Supabase local de verdad con
el `baseline.sql` aplicado — la misma base que tiene quien se auto-hospeda. **Es check obligatorio desde
el 2026-08-08.** **No hay número aquí a propósito**: esta línea ya afirmó un conteo exacto
de specs y "la única que queda fuera", y las dos cosas envejecieron — la suite crece cada semana y la lista de
excepciones cambia con ella. Quién queda fuera es lo que declara la propia variable; léela, no confíes:

```bash
git show origin/main:.github/workflows/e2e.yml | grep -A4 'FORA_DO_CI:'
```

Lo que sigue siendo verdad y es lo que importa: `vps-fresh-onboarding` está entre ellas (WAHA + Redis
+ Resend + Nuvemshop) y es la **P0** de la doctrina de QA — o sea, un `e2e` verde no prueba el recorrido
de instalación desde cero. `followup-journey`, `webhooks` y `capacidades-do-agente` estuvieron fuera y
**volvieron**: hoy corren (`e2e.yml`, listas `SPECS_PARTE_*` — son tres desde el 2026-09-07).

`.github/workflows/publish-image.yml`: `imagens-ok` = las tres imágenes Docker se construyen. **Obligatorio
desde el 2026-08-13.**

**Los cinco son checks obligatorios** en la branch protection de `main` — medido el 2026-08-14 @ `741c4ec8`:

```console
$ gh api repos/melgarafael/DeskcommCRM/branches/main/protection --jq '.required_status_checks.contexts|join(", ")'
verify, build-and-size, invariants, e2e, imagens-ok
```

> Este bloque estuvo equivocado en cuatro puntos hasta el 2026-08-14 (decía "tres checks", "28 de las 32
> specs", "e2e todavía no es obligatorio" y listaba como excluidas tres specs que ya corrían).
> La peor era la del `e2e`: quien la leyera mediría un PR contra la regla equivocada. **Vuelve a contar antes de
> citar** — `ls tests/e2e/*.spec.ts | wc -l` y el comando de arriba.

## Patrones de código (observados en el repo, no inventados)

- **Route handler:** valida el input con Zod → guard (`requireRole` / `requirePlatformAdmin` /
  secret) → query con `organization_id` explícito → `audit()` si es mutación → `ok()` / `fail()`.
- Error: `fail(code, message, status)` con un código de `lib/api/errors.ts`. Nunca un `throw` crudo en el borde.
- JSON **snake_case** en la API. Dinero en `_cents` + `currency`. Fechas ISO-8601 UTC.
- Log: `lib/logger.ts` (estructurado). **`console.log` está prohibido** en código mergeado.
- Tests junto al código (`lib/foo/bar.test.ts`) o en `tests/{unit,api,invariants,e2e}/`.
- Los comentarios de código en PT-BR son la norma en este repo — mantén el idioma del archivo que edites.
  (La documentación de raíz — este archivo, `CLAUDE.md`, `README.md` y compañía — está en español.)

### Marca propia (white-label) — el producto se revende, y el nombre no es tuyo

- **Nunca escribas "Deskcomm"/"DeskcommCRM" en código que llega al usuario.** `tests/unit/branding.test.ts` recorre `app|components|lib|workers|hooks` y lo rechaza; la allowlist **solo se encoge**.
- La marca se resuelve desde la **base de datos** (`platform_branding` para la instalación, `organizations.settings.branding` para la organización). `APP_NAME`/`APP_LOGO_URL`/`APP_ACCENT_HEX` en el `.env` son **semilla y piso de rollback**, no la fuente.
- ¿Necesitas la marca **fuera del DOM** (correo, remitente, ícono, `issuer` del MFA)? Usa `marcaDaSaida()` de `lib/branding/saida.ts` — un hex y un frente legible, tema claro. Nunca le pases `MarcaResolvida` a una plantilla de correo.
- El resolvedor de marca **nunca lanza**: corre en `app/layout.tsx`, y un throw ahí es un 500 en todas las pantallas.
- **El PDF de LGPD no lleva marca** — nombra al controlador (`organizations.legal_name`) y al DPO. Es una decisión, no una omisión; hay un gate en el mapa de arquitectura.
- Contexto de venta en `docs/white-label.md`; mapa en `docs/architecture/marca-propria.architecture.json`.

## Directorios y archivos SENSIBLES

- **`supabase/baseline.sql`** — es lo que aplican `install.sh`/`update.sh` del self-host.
  Todo cambio de schema tiene que aparecer aquí **como apéndice idempotente**; si no,
  no llega a quien ya instaló. Ver la doctrina de Migrations en `CLAUDE.md`.
- **`supabase/migrations/*.sql` ya aplicadas** — nunca las edites. Corrige con una migration nueva.
- **`lib/supabase/admin.ts`** — el service role **se salta la RLS**. 89 rutas lo usan; toda
  query necesita filtrar `organization_id` manualmente, resuelto de una fuente confiable
  (cookie/JWT/webhook secret/path token), **nunca del body**.
- **`lib/auth/public-paths.ts`** — agregar un path aquí quita la verificación de auth de borde.
  Solo con un guard propio dentro de la ruta.
- **`.env*`** — no lo abras, no copies valores, no los registres en logs. Solo `.env.example` es plantilla.
- **`docker-compose.traefik.yml`** — en un VPS que ya tiene su propio proxy inverso
  (Hostinger, Coolify, Dokploy…), es el único lugar que le da al contenedor `app` las labels
  de enrutamiento. Todo `up -d` lleva **los dos** archivos de compose:
  `docker compose -f docker-compose.prod.yml -f docker-compose.traefik.yml --env-file .env up -d app`.
  Olvidar el segundo `-f` recrea el contenedor sin labels: el proxy deja de verlo y el
  dominio entero responde `404`, con el contenedor `healthy` — el healthcheck es un probe TCP
  interno y no sabe nada de enrutamiento. Runbook: `docs/runbooks/deploy.md`.

## Archivos GENERADOS — no editar a mano

- `lib/database.types.ts` (6.1k líneas — generado del schema de Supabase)
- `graphify-out/` (grafo de conocimiento; se regenera con `/graphify .`)
- `pnpm-lock.yaml`, `tsconfig.tsbuildinfo`, `next-env.d.ts`, `.next/`

## Cómo validar un cambio

1. `pnpm typecheck` y `pnpm lint` en cero.
2. `pnpm test:unit` en verde.
3. Tocaste schema/RLS/tabla tenant-aware → `pnpm test:db` (levanta un Postgres efímero vía Docker,
   aplica `baseline.sql` en modo install **y** update, corre los invariantes).
4. Tocaste UI o un flujo de usuario → `pnpm test:e2e` con evidencia visual. **`curl` no cuenta**
   como prueba de UX (doctrina de QA Visual en `CLAUDE.md`).
5. Cambiaste el schema → migration versionada en `supabase/migrations/` **+** apéndice idempotente
   en `supabase/baseline.sql` **+** línea en `supabase/migrations/MANIFEST.md`. Los tres juntos.
6. Creaste una función en `public` → `revoke execute on function ... from public, anon;` y después
   `grant` solo a quien la necesita. Son **dos** orígenes de `EXECUTE` y revocar solo uno deja la
   función expuesta como RPC alcanzable con la anon key. Detalle en `CLAUDE.md`, ítem 9 de la
   doctrina de Migrations.

## Tests existentes (CONFIRMADO)

Medido el 2026-08-14 @ `741c4ec8`, con el comando al lado de cada número:

- **257** archivos de test unitario en `tests/unit/` (`git ls-files 'tests/unit/*.test.ts' 'tests/unit/*.test.tsx' | wc -l`). El repo tiene **491** archivos `*.test.ts(x)` en total (`git ls-files '*.test.ts' '*.test.tsx' | wc -l`) — la diferencia vive junto al código, fuera de `tests/`, y también corre en `test:unit`.
- Archivos de invariantes de base de datos en `tests/invariants/` — RLS/aislamiento cross-tenant, RBAC,
  gobernanza (G1–G6). Excluidos de `test:unit` a propósito; corren vía `pnpm test:db` **y en el job
  `invariants` del CI**. Cuántos: `git ls-files 'tests/invariants/*.test.ts' | wc -l`.
- Specs de Playwright en `tests/e2e/`, casi todas en el CI (vía `e2e.yml`, **obligatorio**). Las que
  quedan fuera están declaradas en `FORA_DO_CI`, **con el motivo escrito al lado**. Esta línea
  ya afirmó "todas menos una" cuando dejó de ser una — por eso ya no cuenta. Ver issue #63.
  Cuántas existen: `ls tests/e2e/*.spec.ts | wc -l`. Cuántas quedan fuera:
  `git show origin/main:.github/workflows/e2e.yml | grep -A4 'FORA_DO_CI:'`.

> **Los dos números salieron de aquí, y es decisión, no descuido.** Estaban en 102 y 46/45 cuando lo
> medido era 114 y 51/50 — envejecieron porque cada entrega que agrega un test los falsea,
> y ningún gate lee prosa. Donde la afirmación puede volverse comando, se vuelve comando: un comando no envejece.
> Lo que sigue vigilado por un gate es lo que importa — `tests/unit/e2e-cobertura-completa.test.ts`
> rechaza toda spec nueva que no esté en `SPECS_PARTE_*` o en `FORA_DO_CI` con motivo escrito.

## Limitaciones conocidas (estado al 2026-07-29, contra `origin/main` @ 789dfa6)

- **1 de las 46 specs E2E sigue fuera del CI** (`vps-fresh-onboarding`), y el `e2e` **es** check
  obligatorio desde el 2026-08-08. O sea: un PR que rompa el `e2e` no entra — pero el recorrido de
  instalación desde cero, que es el producto que se vende, sigue sin gate. Si lo tocaste, la
  prueba es tuya. *(Corregido el 2026-08-14; la redacción anterior — "4 de las 32, no obligatorio" —
  cambiaba la regla de cualquier triage que la leyera.)*
- Rate limit HTTP: `lib/auth/rate-limit.ts` cubre **login, signup, recuperación de contraseña y
  aceptación de invitación** (contando por IP **y** por identificador hasheado); `checkRateLimit` cubre
  el webhook de captación y el dispatcher de IA. **Los crons y el MCP siguen sin él.** Mide antes de actuar:
  `grep -rln 'authRateLimited\|checkRateLimit(' app lib --include='*.ts' --include='*.tsx'`.
  Esta línea decía "existe en 2 puntos; login y signup no lo tienen" — era el estado anterior al
  issue #64, y `docs/threat-model.md` todavía carga la versión vieja, con nota de reauditoría.
- El fallback del rate limit es **en memoria** — sin Upstash configurado el límite es por proceso.
- `Idempotency-Key` implementado en **1** ruta, aunque el contrato lo promete en los POST de creación.
- **`.env.example` está completo** — medido el 2026-08-14: de las 45 claves de `lib/env.ts`, la
  única ausente es `NODE_ENV`, que no es configuración del operador. Esta línea decía que faltaban
  6, "incluidos 3 secrets"; los tres (`IMPERSONATE_COOKIE_SECRET`, `INTERNAL_CRON_SECRET`,
  `LGPD_SIGNING_KEY`) están ahí. Si agregas una env var, agrégala en los dos lugares (ítem 9 del
  DoD) — la regla sigue vigente, lo que se saldó fue la deuda.
- `lib/auth/invite-token.ts` cae en `"dev-fallback"` como secret HMAC si no existe ningún secret
  (inalcanzable en producción, porque `INTERNAL_SECRET` es obligatorio y tumba el arranque).
- **89 de los 169 handlers de `app/api/**` usan service role** — sin gate automático para el filtro de
  `organization_id`. Si escribes un handler nuevo, el filtro es tu responsabilidad.
- Detalles y prioridad: [`docs/harness-audit.md`](docs/harness-audit.md),
  [`docs/current-state.md`](docs/current-state.md) y [`docs/threat-model.md`](docs/threat-model.md).

## Reglas de seguridad

- Siempre `getUser()` en el backend. **Nunca `getSession()`** (confía en la cookie sin revalidar).
- API key/token **nunca** en query string — solo en header. El plaintext del bearer se muestra
  **una vez**; en la base de datos solo el hash SHA256.
- HMAC de webhook con `crypto.timingSafeEqual`. Fail-closed cuando falta el secret.
- Nunca registres en logs un secreto, token, CPF, teléfono o correo. Sentry tiene un `beforeSend` que
  sanitiza — no confíes en él como única capa.
- No hagas commit de screenshots/dumps con datos reales de clientes.

## Packaging — si tocaste `Dockerfile*`, `docker-compose*.yml` o `setup-kit/`

Ley completa en [`docs/doctrine/packaging.md`](docs/doctrine/packaging.md). Lo no negociable:

- **Ningún servicio de `docker-compose.prod.yml` construye en la máquina del cliente.** Todo servicio
  declara `image:` de una imagen publicada; `build:` solo existe **al lado**, como escape.
  Un servicio solo-`build:` lo salta `docker compose pull` y es inmune a `up -d` sin `--build` —
  no solo es caro de instalar, es que **nunca se actualiza**.
- **Publicar es un acto del CI**, nunca de tu máquina: un build ARM local no corre en el VPS amd64.
- **La instalación del cliente apunta a un número de versión**, nunca a un tag móvil. Aquí `latest`
  significa **la punta de `main`**, no la última release — quien quiere la última release usa `stable`.
- **Una dependencia upstream se referencia con tag fijo, nunca se republica** (WAHA es licenciado).
- **Un bump de versión no puede exigir que el operador del VPS edite archivos a mano.**

`pnpm test:shell` es el único gate que ejercita el kit. Córrelo.

## Criterio de terminado

Vale la **Definition of Done de [`CLAUDE.md`](CLAUDE.md)** — cuenta ahí en vez de confiar en un número aquí (`sed -n '/^## Definition of Done/,/^¿Un staff engineer/p' CLAUDE.md | grep -cE '^[0-9]+\. '`; esta línea ya dijo 15 cuando el DoD tenía 16). La regla tiene que DELIMITAR la sección: la primera versión de esta línea ofrecía `grep -c '^[0-9]\+\. \*\*' CLAUDE.md`, que devuelve **25** — casa con toda línea numerada en negritas del archivo (anti-patterns, packaging, higiene de branches, migrations) y pierde los ítems 1–10 del propio DoD, que no van en negritas. Cambiar el número por el comando solo ayuda si el comando responde la pregunta. No declares nada terminado
sin: typecheck/lint en cero, tests relevantes en verde, RLS probada si tocaste una tabla
tenant-aware, migration + baseline + MANIFEST si cambiaste el schema, prueba visual si cambiaste UI, y la
regla de packaging de arriba si cambiaste el artefacto que instala quien se auto-hospeda.

## Regla final — no inventes

Este repositorio tiene PRDs, specs, reglas de negocio y doctrina escritos
(`docs/prd/`, `docs/specs/`, `docs/business-rules/`, `docs/doctrine/`).
**Nunca inventes una regla de negocio, número, SLA o comportamiento de producto.**
Si la regla no está escrita, di que no está y pregunta — no llenes el hueco con una
suposición plausible. Al documentar, marca lo que está `CONFIRMADO` (probado por código) y lo
que es `INFERIDO`.

<!-- BEGIN:nextjs-agent-rules -->

# This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->
