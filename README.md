<div align="center">

🇪🇸 Español · [🇺🇸 English](README.en.md) · [🇧🇷 Português](README.pt-BR.md)

# 🛠️ DeskcommCRM — el Sistema Operativo de Ventas con IA, open source, para WhatsApp

**Agentes de IA que atienden, califican y venden por WhatsApp — dentro de un CRM open source que corre en tu propio servidor.**
**Sin mensualidad, sin funciones bloqueadas, tus datos siguen siendo tuyos. La alternativa abierta a Kommo, Octadesk e Intercom.**

[![Next.js 16](https://img.shields.io/badge/Next.js-16-black?logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178c6?logo=typescript)](https://www.typescriptlang.org)
[![Supabase](https://img.shields.io/badge/Supabase-Postgres%2BAuth%2BStorage-3ecf8e?logo=supabase)](https://supabase.com)
[![Self-hosted](https://img.shields.io/badge/self--hosted-1%20comando-orange)](setup-kit/)
[![CI](https://github.com/melgarafael/DeskcommCRM/actions/workflows/ci.yml/badge.svg)](https://github.com/melgarafael/DeskcommCRM/actions/workflows/ci.yml)
[![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE)

[**⚡ Instalar**](#-instalar-en-tu-vps-el-camino-principal) · [**🔄 Actualizar**](#-actualizar) · [**🧭 Visión**](VISION.md) · [**🏗️ Arquitectura**](ARCHITECTURE.md) · [**🤝 Contribuir**](CONTRIBUTING.md) · [**🗺️ Roadmap**](#%EF%B8%8F-roadmap)

</div>

---

> ### ☁️ Corre este CRM en producción con 1 comando
>
> DeskcommCRM instala el CRM completo (app + WhatsApp + base de datos) en un VPS con un solo
> comando. **Hostinger es el hosting recomendado** para este camino.
>
> **[👉 Hosting de Hostinger con descuento](https://www.hostg.xyz/SHJ5B)** — usa el cupón
> **`THALENA`** para obtener **10% OFF en los planes anuales de VPS**. Válido para cuentas nuevas;
> si ya tienes una cuenta, crea otra con un correo distinto para acceder al descuento exclusivo.
>
> **¿Todavía no tienes servidor?** Ejecuta esto **en tu computadora** (macOS, Linux o WSL). Te
> muestra los recursos mínimos del VPS — con los números del runbook, no un "depende" — y te
> devuelve el comando correcto para tu caso:
>
> ```bash
> git clone https://github.com/melgarafael/DeskcommCRM.git
> cd DeskcommCRM
> bash setup-kit/comecar.sh
> ```
>
> *(¿prefieres leer antes de ejecutar? el comando anterior ya descarga el repositorio; el script
> no instala nada sin que lo confirmes.)*

---

## ⚡ Instalar en tu VPS (el camino principal)

### 1. Entra a tu VPS

Abre la **Terminal** en tu computadora (en Windows, **PowerShell**; en Mac o Linux, la
**Terminal**) y conéctate con la IP y el puerto que tu hosting te envió por correo:

```bash
ssh -p PUERTO root@TU_IP
```

Reemplaza `PUERTO` y `TU_IP` por los tuyos. Si tu hosting no mencionó ningún puerto, es el que
viene por defecto (22) y puedes omitirlo: `ssh root@TU_IP`.

Te va a pedir la contraseña. **Mientras escribes, no aparece nada en pantalla — ni asteriscos.**
No es que se haya trabado: la terminal está ocultando tu contraseña. Escríbela (o pégala) y
presiona Enter.

> En la primera conexión pregunta `Are you sure you want to continue connecting?` — responde
> `yes`. Es el servidor presentándose por primera vez.

### 2. Ejecuta el instalador

Ya dentro del VPS:

```bash
git clone https://github.com/melgarafael/DeskcommCRM.git
cd DeskcommCRM
bash setup-kit/install.sh
```

Eso es todo. **No instalas Node, ni pnpm, ni compilas nada** — la imagen de la app ya viene
lista. Si falta Docker, el instalador pregunta y lo instala solo.

### Lo que necesitas tener a mano

| Elemento | Dónde conseguirlo |
|---|---|
| **VPS con Docker** | [Hostinger](https://www.hostg.xyz/SHJ5B) — cupón `THALENA`: 10% OFF en planes anuales para cuentas nuevas. O cualquier VPS con Docker; se recomiendan 4 GB de RAM. |
| **Dominio** | Un registro **A** apuntando a la IP del VPS (ej.: `crm.tuempresa.com`) |
| **Base de datos** | Cuenta gratis en [supabase.com](https://supabase.com) — 3 claves + la cadena de conexión del **Session pooler** |
| **IA** | Una clave de **OpenRouter**, **Anthropic** u **OpenAI** — el instalador pregunta cuál quieres |
| **WhatsApp** | Tu número, conectado por código QR en el onboarding (o el canal oficial de Meta) |

> 💡 **Supabase lo puede crear el propio instalador.** Exporta un `SUPABASE_ACCESS_TOKEN` antes
> de ejecutarlo y él crea el proyecto, espera a que la base de datos quede saludable, obtiene las
> 4 credenciales y descubre el host del pooler probando una conexión real — sin copiar y pegar.

### Lo que el instalador hace por ti

**Pregunta solo lo que es tuyo** (dominio, claves, contraseña del admin), **valida cada
respuesta antes de seguir** — una clave equivocada la rechaza en el momento, no tres pasos
después — y se encarga del resto:

1. Genera todos los secretos técnicos por su cuenta (tú no inventas ninguna contraseña).
2. Crea las extensiones de Postgres y aplica el schema completo (`supabase/baseline.sql`).
3. Crea el primer admin con el correo y la contraseña que elegiste.
4. Levanta todo el stack con **HTTPS automático** y verifica la salud al final.
5. Instala el **cron de las automatizaciones** (sin él, las reglas CUANDO/SI/ENTONCES se quedan
   detenidas en la cola) y el **agente de actualización**, que es lo que hace que exista el botón
   "Actualizar ahora" en la pantalla.

**Volver a ejecutarlo no rompe nada** — `install.sh` es idempotente: no duplica el cron, no
vuelve a crear el usuario y retoma donde se quedó.

> **Modo no interactivo:** copia `.env.vps.example` a `.env`, complétalo y ejecuta
> `bash setup-kit/install.sh --yes`.

### Hostinger y otros hostings

Funciona. Si tu VPS ya viene con un **proxy inverso propio** ocupando los puertos 80/443, el
instalador **lo detecta solo** y publica el CRM a través de él, en lugar de intentar levantar un
Caddy que no cabría. En un caso específico — proxy en `--network host`, como hace Hostinger —
**pregunta en vez de adivinar**, porque publicar detrás del proxy equivocado instala "con éxito"
un sitio mudo. Detalles en [`setup-kit/README.md`](setup-kit/README.md).

### Primer acceso

Abre `https://<tu-dominio>` (el candado tarda ~1 min en aparecer), entra con el admin y ten a la
mano **Google Authenticator** o **Authy** *si* quieres activar la verificación en dos pasos — es
**opcional** y está en Configuración › Seguridad; el primer inicio de sesión **no** la exige. En
el onboarding, escanea el código QR con el WhatsApp de tu número.

### 🤖 ¿Prefieres que una IA lo instale por ti?

Clona el repositorio en el VPS y pídele a **Claude Code**: *"instálame DeskcommCRM"*.
Puede seguir el [`setup-kit/README.md`](setup-kit/README.md)
— que trae el paso a paso y las trampas ya identificadas — y conducir todo el proceso.

---

## 🔄 Actualizar

¿Salió una versión nueva? Hay dos caminos, y el primero **no requiere terminal**.

### Desde la pantalla (recomendado)

Cuando hay una versión nueva, el pie del menú lateral muestra **"Nueva versión"** — solo para el
dueño del servidor, porque avisarle a quien no puede actualizar es ruido. Haz clic y llegarás a
**Configuración → Actualización**, que muestra qué cambia, **hace el respaldo de la base de datos
por su cuenta** y acompaña cada fase (respaldo → código → base de datos → en línea) hasta
terminar. Nada de SSH.

Si la versión nueva arranca rota, el agente **regresa solo a la imagen anterior** y guarda ese
regreso en el `.env` — sin eso, el siguiente reinicio traería la app rota de nuevo, en silencio.

> Por debajo: la app solo registra la solicitud; quien la ejecuta es el agente que `install.sh`
> dejó en tu VPS, en un cron que revisa **cada 5 minutos** — así que la actualización empieza a
> más tardar 5 minutos después del clic. Si ese agente está caído, la pantalla avisa
> **"Actualización automática no disponible"** y muestra el comando de abajo — no finge que
> funcionó.

### Desde la terminal

```bash
cd /ruta/al/DeskcommCRM
bash setup-kit/update.sh
```

El comando hace, en este orden: (1) verifica si de verdad hay una versión nueva — si no la hay,
sale de inmediato; (2) **respalda la base de datos antes de tocar cualquier cosa**; (3) descarga
el código nuevo; (4) actualiza la base de datos volviendo a aplicar `baseline.sql`, que es
idempotente y **auto-curativo** (repara por su cuenta datos que versiones antiguas dejaron
inconsistentes); (5) descarga la imagen nueva de la app; (6) verifica la salud al final.

**El objetivo es la última versión publicada** (`v1.2.3`), no la punta de `main` — actualizar
siempre lleva a una versión etiquetada y descrita en el [`CHANGELOG.md`](CHANGELOG.md), nunca a
un commit sin probar. **Se niega** a regresar a una versión anterior a la instalada (eso apagaría
cosas que ya tienes); para eso existe `--force`, a propósito.

**Cosas normales que vas a ver:** un montón de `already exists` / `multiple primary keys` en la
parte de la base de datos — **es esperado e inofensivo**, son cosas que ya existían. El script
filtra ese ruido y muestra `✓ banco atualizado`. Si aparece `⚠ avisos que não são os esperados`,
ahí sí guarda el mensaje.

**¿Algo salió mal?** `bash setup-kit/restore.sh` regresa al respaldo.
**¿Solo quieres diagnosticar?** `bash setup-kit/healthcheck.sh`.

> ⚠️ **En una instalación antigua que todavía no tiene el agente de la pantalla**, ejecuta
> `update.sh` **dos veces**: la primera ejecución todavía es la del script viejo (que descarga el
> nuevo); la segunda instala el agente y activa el botón.

Paso a paso en lenguaje sencillo: [`docs/ATUALIZANDO.md`](docs/ATUALIZANDO.md).

### Otros comandos del kit

| Script | Función |
|---|---|
| `install.sh` | Instala todo (idempotente — puedes volver a ejecutarlo) |
| `update.sh` | Actualiza a la versión nueva, con respaldo automático |
| `backup.sh` | Respaldo de la base de datos + sesiones de WhatsApp |
| `restore.sh` | Restaura un respaldo |
| `reset-password.sh` | Restablece la contraseña de un usuario |
| `reset-mfa.sh` | Quita el MFA de quien perdió el celular |
| `healthcheck.sh` | Diagnóstico de todos los servicios a la vez |

> **El respaldo importa:** el plan gratuito de Supabase **no hace respaldos por sí solo**. Vale la
> pena programar `backup.sh` a diario en el cron. `update.sh` ya hace un respaldo antes de cada
> actualización.

---

## ✨ Qué es

**Deskcomm** viene de **Desk** (escritorio) + **comm** (comercio): **el comercial de escritorio** — toda la operación de ventas de tu negocio en un solo escritorio, operada por personas y agentes de IA trabajando juntos.

El proyecto nació como CRM de e-commerce y la comunidad lo llevó mucho más lejos: hoy corre en **clínicas, inmobiliarias, infoproductos, agencias, tiendas y prestadores de servicios** — cualquier negocio que venda por WhatsApp. El producto acompañó ese giro y se convirtió en un **sistema operativo de ventas**: agentes de IA con RAG por tenant atienden, califican, mueven leads en el embudo, disparan automatizaciones y saben cuándo pasarle la conversación a una persona — con todo el CRM expuesto vía **MCP** para que los agentes lo operen de verdad. La historia completa está en [`VISION.md`](VISION.md).

### Diferenciales

- 🤖 **Agentes de IA que operan el CRM** — RAG por tenant, skills que el agente ejecuta por su cuenta durante la atención, memoria de la operación, análisis de sentimiento, handoff IA→humano auditado, la IA como responsable (assignee) de primera clase y tope de gasto por organización. No es un chatbot decorativo: el agente atiende, califica y mueve el embudo.
- 🔁 **Nada muere en silencio** — follow-up que retoma la conversación que se enfrió (con tiempo adaptativo y disparadores por etapa del embudo), radar de lo que corre riesgo de morir sin respuesta, y central de avisos para lo que necesita una decisión humana.
- 🧠 **Agentes que se mejoran a sí mismos** — las conversaciones resueltas se vuelven conocimiento nuevo; la pantalla de **Evolución de la IA** muestra si el agente está mejorando, dónde se equivoca y qué falta enseñarle; las **Propuestas** son mejoras que la IA sugiere para sí misma, aplicables como versión nueva — siempre con aprobación humana.
- 🧩 **Multinicho por diseño** — vocabulario configurable por embudo: lead se vuelve *Cliente*, *Paciente* o *Comprador*; "ganado" se vuelve *Pagado*, *Agendado* o *Cerrado*. El mismo núcleo sirve para e-commerce (nuestra cuna, con integración Nuvemshop), clínicas, inmobiliarias o infoproductos.
- 💬 **WhatsApp de dos formas** — por **código QR** (WAHA, multinúmero, con anti-baneo: throttle + jitter + ventana horaria) o por el **canal oficial de Meta** (Cloud API, con plantillas aprobadas y sincronizadas). Medios vía Storage, detección de STOP.
- 🔀 **Elige tu IA** — OpenRouter, Anthropic u OpenAI, decidido en la instalación y cambiable después desde la pantalla, **por parte del sistema** (lo que conversa no tiene que ser lo que indexa).
- 👥 **Gobernanza de atención** — RBAC del lado del servidor de verdad, asignación/transferencia auditada, cola con rotación, enrutamiento automático por intención y alcance de visualización por rol.
- 🏢 **Multi-tenant + privacidad por diseño (LGPD)** — RLS en toda tabla tenant-aware con test de aislamiento como gate de CI; anonimización preferida sobre borrado; audit log append-only con retención de 5 años.
- 🖥️ **Self-hosted de verdad** — tus datos en tu VPS; instalación y actualización con 1 comando (o 1 clic); sin versión de pago, sin funciones bloqueadas.

### 🔌 Webhooks y automatizaciones

Cada tenant puede crear **fuentes de captación**: una dirección pública (`/api/v1/webhooks/in/<token>`) que recibe leads de landing pages, formularios propios o herramientas como Zapier/n8n vía POST (JSON o `application/x-www-form-urlencoded`) y los mete directo al embudo/etapa elegido — sin código, sin integración a la medida por tenant. Sobre esas fuentes (y los demás eventos del CRM — el lead cambió de etapa, recibió una etiqueta, llegó un mensaje de WhatsApp), el tenant arma **automatizaciones**: reglas con el formato CUANDO/SI/ENTONCES que disparan acciones como agregar una etiqueta, mover el lead en el embudo, asignarlo a un agente, enviar un mensaje de WhatsApp o avisar a otro sistema vía webhook de salida.

En la interfaz, todo vive en **Webhooks** en la barra lateral (visible solo para quien tiene rol `manager`/`admin`). La pantalla tiene tres pestañas: **Recibir datos** (crear una fuente, copiar la dirección/formulario listo, disparar un lead de prueba, ver las últimas recepciones), **Automatizaciones** (armar la regla, que siempre nace pausada hasta que la revises y la actives) y **Actividad** (línea de tiempo de cada ejecución, con el resultado de cada acción y reenvío manual cuando una llamada externa falla).

Por debajo, cada evento se convierte en una fila en `event_log` — ningún trigger de base de datos hace llamadas HTTP directamente. Quien vacía esa cola es la ruta `/api/v1/cron/event-log-drain`, llamada cada minuto. **`install.sh`/`update.sh` ya configuran ese cron por su cuenta** — sin él, las automatizaciones se crean normalmente pero nunca se ejecutan.

---

## 🖥️ Lo que operas (las pantallas)

| Grupo | Pantallas |
|---|---|
| **Atención** | **Inbox** (conversaciones de WhatsApp, tú y la IA lado a lado) · **Radar** (quién se enfrió y sigue abierto) · **Respuestas rápidas** |
| **CRM** | **Kanban** (dónde está cada negocio en el embudo) · **Contactos** · **Embudos** (etapas, vocabulario del negocio y motivos de pérdida) |
| **Agente de IA** | **Agentes** · **Follow-ups** · **Enrutadores** · **Proveedores** y **Credenciales** · **Conocimiento** (RAG) · **Memoria** · **Skills** · **Casos** · **Alertas** · **Propuestas** · **Ejecuciones** · **Uso y presupuesto** |
| **Canales** | **Conexiones** (QR o canal oficial de Meta, con salud, reconexión y plantillas) · **Nuvemshop** · **Webhooks** |
| **Análisis** | **Desempeño** (embudo y rendimiento por agente) · **Evolución de la IA** · **Audit Log** |
| **Organización** | **Equipo** · **Distribución de atención** · **Organización** · **LGPD** · **API Tokens** · **Seguridad** (MFA, códigos de recuperación, sesiones) · Perfil, Notificaciones, Facturación |

Toda pantalla tiene una puerta en la navegación — el CI rechaza una pantalla que existe pero a la que solo se llega escribiendo la URL.

---

## 🧱 Stack

| Capa | Elección | Por qué |
|---|---|---|
| **Frontend** | Next.js 16 App Router (Turbopack) + React 19 + TypeScript 6 estricto | Server Components + Route Handlers en el mismo repo |
| **Estilo** | Tailwind + shadcn/ui (`new-york`, neutral) | Personalizable sin lock-in |
| **DB** | Supabase (Postgres + RLS + `vector`) | Multi-tenant nativo, embeddings para RAG |
| **Auth** | Supabase Auth vía `@supabase/ssr` | Cookie SameSite=Strict, HttpOnly |
| **Realtime** | Supabase Realtime | postgres_changes + broadcast |
| **Storage** | Supabase Storage (URLs firmadas) | Bucket privado `whatsapp-media` |
| **WhatsApp** | WAHA Plus (motor NOWEB) + Meta Cloud API | QR para empezar rápido; canal oficial para escalar |
| **Colas** | Tabla `event_log` + workers (cron) | Un trigger de base de datos nunca hace HTTP |
| **Rate limit** | Upstash Redis (sliding window) | Serverless, el plan gratuito alcanza |
| **IA** | Vercel AI SDK v7 — OpenRouter, Anthropic, OpenAI y Google | El instalador pregunta cuál; se cambia después desde la pantalla |
| **Validación** | Zod | Input externo, env, payloads |
| **Observabilidad** | Sentry (scrub en error, transacción, span y breadcrumb) | Telemetría opt-in en la instalación |
| **Hosting** | VPS con Docker (Hostinger recomendada) | App + WhatsApp + workers en tu máquina |

Detalles: [`ARCHITECTURE.md`](ARCHITECTURE.md).

---

## 🧑‍💻 Desarrollo (solo para contribuir al código)

> ⚠️ **Si quieres USAR el CRM, no es aquí** — usa el [instalador del VPS](#-instalar-en-tu-vps-el-camino-principal).
> Esta sección es para quien va a modificar el código.

```bash
git clone https://github.com/melgarafael/DeskcommCRM.git
cd DeskcommCRM

nvm use                     # Node 22
npm install -g pnpm && pnpm install

cp .env.example .env.local  # guía completa en docs/SETUP.md

docker compose up -d        # WAHA local (opcional en dev sin WhatsApp)

# Schema: aplica el baseline, NO las migrations.
# Las migrations 0001-0009 y 0013 son stubs `SELECT 1;` — la cadena no levanta desde cero.
# El schema real vive en baseline.sql, el mismo que install.sh aplica en el VPS.
# `supabase db push` "pasa" y te deja la base de datos vacía.
supabase link --project-ref <tu-ref>

# En un proyecto de Supabase NUEVO, habilita antes las extensiones que usa el schema —
# sin ellas el baseline se detiene en `type public.vector does not exist`.
psql "$SUPABASE_DB_URL" -v ON_ERROR_STOP=1 -c \
  'create extension if not exists vector with schema public;
   create extension if not exists citext with schema public;
   create extension if not exists pg_trgm with schema public;'

psql "$SUPABASE_DB_URL" -v ON_ERROR_STOP=1 -f supabase/baseline.sql

pnpm dev
```

App: <http://localhost:3000> · Health check: <http://localhost:3000/api/v1/health>

[`docs/SETUP.md`](docs/SETUP.md) es el tutorial completo de **todas las integraciones** (Supabase, WAHA, proveedores de IA, Upstash, Sentry, Resend, Nuvemshop) — ~60–90 min desde cero hasta la app corriendo. *(Ese documento todavía está en portugués.)*

---

## 📁 Estructura

```
DeskcommCRM/
├── app/                    # Next.js App Router
│   ├── (admin)/            # Rutas de super-admin (impersonate, tenants)
│   ├── (public)/           # Login, recuperación
│   ├── app/                # Rutas autenticadas: inbox, radar, kanban, contacts,
│   │                       #   connections, ai/*, integrations, metrics, lgpd,
│   │                       #   audit, team, settings
│   └── api/v1/             # API REST canónica (196 route handlers)
├── components/             # React (ui/, inbox/, kanban/, shell/, ...)
├── lib/                    # supabase/, waha/, channels/, ai/, agent-engine/,
│                           #   api/, routing/, navigation/, env.ts
├── workers/                # consumidores de event_log (IA, RAG, LGPD, medios, rutinas)
├── supabase/migrations/    # SQL versionado (+ baseline.sql para el self-host)
├── tests/{e2e,unit,invariants,shell}/
├── scripts/                # seeds, qa-waves, mantenimiento
├── docs/                   # PRDs, specs, runbooks, SETUP.md, ATUALIZANDO.md
├── setup-kit/              # camino público de instalación y actualización self-host
```

---

## 🧪 Tests

```bash
pnpm typecheck     # tsc --noEmit (estricto)
pnpm lint          # eslint next/core-web-vitals
pnpm test:unit     # Vitest (NO incluye tests/invariants/**)
pnpm test:db       # Postgres efímero + baseline install/update + invariantes
pnpm test:e2e      # Playwright (requiere dev server)
```

**Estos checks son obligatorios** para hacer merge en `main`. La lista de abajo ya dijo "cuatro" y después "cinco" — **mídela, no confíes en ella**:

```bash
gh api repos/melgarafael/DeskcommCRM/branches/main/protection \
  --jq '.required_status_checks.contexts|join(", ")'
# el 2026-08-14: verify, build-and-size, invariants, e2e, imagens-ok
```


| Check | Qué hace |
|---|---|
| `verify` | typecheck + lint + `lint:channels` + `test:unit` + `test:shell` |
| `invariants` | levanta un Postgres limpio, aplica `baseline.sql` en modo **install** y después en modo **update** — las dos pasadas con `ON_ERROR_STOP=1`, que es lo que convierte la segunda en una prueba de idempotencia y no solo en un "terminó" —, y corre los invariantes de RBAC, asignación, alcance, enrutamiento, follow-up, webhooks y automatizaciones |
| `build-and-size` | `pnpm build` en Node 22 |
| `e2e` | levanta un Supabase local, aplica `baseline.sql` y corre **48 de las 49 specs** de Playwright por el frontend |
| `imagens-ok` | falla cuando cualquiera de las tres imágenes Docker (`app`, `worker`, `scheduler`) no se construye — es el artefacto que instala quien se auto-hospeda |

La única spec fuera del `e2e` es `vps-fresh-onboarding` — necesita WAHA + Redis + Resend + Nuvemshop de verdad. Es la **P0** de nuestra doctrina de QA visual, así que un `e2e` verde **no** prueba el recorrido de instalación desde cero; ese se prueba en un VPS.

Entre los invariantes está el **test de aislamiento RLS**: crea 2 organizaciones, simula los claims JWT por el mismo camino `auth.uid()` / `fn_user_org_ids()` que usan las policies de producción, y prueba que un usuario de la org A ve **cero filas** de la org B en `conversations`, `messages`, `contacts` y `crm_leads`. Antes de eso, un caso de control prueba que las filas de la org B realmente existen — sin él, el test pasaría con la tabla vacía.

---

## 📚 Documentación

| Doc | Qué contiene |
|---|---|
| [`setup-kit/README.md`](setup-kit/README.md) | **Instalación self-host** — el kit, los scripts y Hostinger |
| [`docs/ATUALIZANDO.md`](docs/ATUALIZANDO.md) | **Cómo actualizar** tu instalación, en lenguaje sencillo |
| [`VISION.md`](VISION.md) | **Visión y posicionamiento** — qué es el proyecto, en qué cree y hacia dónde va |
| [`CHANGELOG.md`](CHANGELOG.md) | Qué cambió en cada versión — **lee la sección de la versión antes de actualizar** |
| [`docs/SETUP.md`](docs/SETUP.md) | Setup de desarrollo, paso a paso de todas las integraciones |
| [`docs/white-label.es.md`](docs/white-label.es.md) | **Instalar para clientes** — cambiar la marca, una instalación por cliente vs. compartida, reventa |
| [`docs/runbooks/deploy.md`](docs/runbooks/deploy.md) | Deploy en producción |
| [`CLAUDE.md`](CLAUDE.md) | Convenciones no negociables (lectura obligatoria para contribuir) |
| [`ARCHITECTURE.md`](ARCHITECTURE.md) | Visión de 1 página de la arquitectura |
| [`docs/index.md`](docs/index.md) | Índice de los 157 documentos, con regla de precedencia |
| [`docs/prd/`](docs/prd/) · [`docs/specs/`](docs/specs/) | PRDs y specs técnicas (schema SQL, payloads, MCP, gobernanza) |

> Los documentos de raíz (`README.md`, `CLAUDE.md`, `AGENTS.md`, `VISION.md`, `ARCHITECTURE.md`,
> `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`) y el índice `docs/index.md` están en español. El resto de
> `docs/` todavía está en portugués de Brasil, el idioma del proyecto original.

---

## 🤝 Contribuir

Este proyecto es open source para la comunidad. Toda contribución es bienvenida — desde corregir un error de dedo en la documentación hasta una función nueva.

**Antes de abrir un PR:**

1. Lee [`CLAUDE.md`](CLAUDE.md) (~5 min) — convenciones no negociables (multi-tenancy, RLS, auditoría, LGPD).
2. Lee [`CONTRIBUTING.md`](CONTRIBUTING.md) — flujo de branches, commits, epic-executor.
3. Sigue el [Código de Conducta](CODE_OF_CONDUCT.md).

**Flujo corto:**

```bash
git checkout -b feat/short-slug
# implementa + tests
pnpm typecheck && pnpm lint && pnpm lint:channels && pnpm test:unit && pnpm test:shell && pnpm build
pnpm test:db   # necesita Docker — es el job `invariants`, obligatorio para el merge
git commit -m "feat(alcance): descripción"
# abre el PR — la plantilla ya trae el checklist de Definition of Done
```

Esas dos líneas son **todo lo que se puede correr en tu máquina**, a propósito: correr solo la
mitad y descubrir el resto como una sorpresa en rojo después de horas de espera es la peor primera
experiencia que este repositorio puede dar.

Dos gates obligatorios **no** caben ahí y solo corren en el CI: `e2e` (necesita un Supabase local)
e `imagens-ok` (construye las tres imágenes Docker). Verde en tu máquina no es verde en el merge.

**Definition of Done:** typecheck en cero, lint en cero, tests relevantes en verde, RLS probada si toca una tabla tenant-aware, audit log emitido en las mutaciones, migration versionada **+ apéndice en `baseline.sql`** si cambia el schema (si no, el cambio no llega a quien se auto-hospeda). Detalles en [`CLAUDE.md`](CLAUDE.md#definition-of-done).

---

## 🐛 Reportar bugs

Abre un [issue](https://github.com/melgarafael/DeskcommCRM/issues/new/choose) — la plantilla pide lo que necesitamos (entorno, `/api/v1/health`, pasos). Ejecutar `bash setup-kit/healthcheck.sh` y pegar la salida ayuda mucho.

Para **vulnerabilidades de seguridad**, **NO abras un issue público** — usa el [reporte privado de vulnerabilidades](https://github.com/melgarafael/DeskcommCRM/security/advisories/new). Detalles en [`SECURITY.md`](SECURITY.md).

---

## 🗺️ Roadmap

### ✅ Entregado

- **Fundación y plataforma** — auth (MFA para admin), multi-tenancy con RLS + test de aislamiento, RBAC de 4 roles, audit log append-only, onboarding de tenant.
- **Atención por WhatsApp** — inbox de 3 paneles en tiempo real, conexiones multinúmero por **QR (WAHA)** o **canal oficial de Meta** (plantillas aprobadas y sincronizadas), medios vía Storage, anti-baneo (throttle + jitter + ventana horaria), detección de STOP.
- **CRM y pedidos** — kanban con vocabulario configurable por nicho (fractional indexing), gestión de embudos desde la pantalla, customer 360, contactos, etiquetas, integración Nuvemshop.
- **IA nativa** — agentes con RAG por tenant (pgvector), **skills** que el agente ejecuta por su cuenta, **memoria de la organización**, enrutador de intención por número, análisis de sentimiento, handoff IA→humano, tope de gasto por organización, servidor MCP interno.
- **Elección de proveedor de IA** — OpenRouter, Anthropic u OpenAI, decidido en la instalación y cambiable por parte del sistema desde la pantalla.
- **Follow-up vivo** — retoma de conversaciones enfriadas con tiempo adaptativo, disparadores por etapa del embudo y por caso, cola con rotación, y el Radar de lo que corre riesgo de morir sin respuesta.
- **LGPD** — exportación y borrado (redact) vía workers, anonimización en cascada, consentimiento auditado.
- **Self-host** — `setup-kit` (app + WhatsApp + base de datos con 1 comando), `baseline.sql` auto-curativo, **actualización desde la pantalla** con respaldo automático, runbook de producción.
- **Webhooks y automatización** — fuentes de captación + reglas CUANDO/SI/ENTONCES + disparadores hacia sistemas externos.
- **Gobernanza de atención** — RBAC del lado del servidor en toda la API, asignación y transferencia auditadas (la IA como responsable de 1ª clase), visualización por rol (RLS) + métricas por agente, enrutamiento automático con cola y panel de gestión, y contrato de gobernanza para agentes de IA externos ([`docs/specs/14`](docs/specs/14-contrato-governanca-agentes-externos.md)).
- **Operación visible** — motivo de la retención anti-baneo explicado en la conversación, central de avisos con severidad, aviso de mensaje atorado, control de protección de envío (ventana/ritmo/tope), capacidades declaradas del agente y propuestas del flywheel aplicables como versión nueva (con aprobación humana).

### 🔮 Próximo

- **MCP público** — capacidades del CRM expuestas al ecosistema de agentes: conecta el agente que quieras y opera Deskcomm.
- **Plantillas por nicho** — embudos y vocabularios listos para clínicas, inmobiliarias, infoproductos y servicios (e-commerce ya entregado).
- **Integraciones** — VTEX y Shopify vía adapter pattern (Nuvemshop ya entregado).
- **Identidad probabilística** — unificación de contactos entre canales.

---

## 💬 Comunidad

- **Discusiones:** [GitHub Discussions](https://github.com/melgarafael/DeskcommCRM/discussions) — para preguntas, ideas y casos de uso.
- **Issues:** [GitHub Issues](https://github.com/melgarafael/DeskcommCRM/issues) — bugs y tareas.
- **Instagram:** [@melgarafael](https://www.instagram.com/melgarafael)
- **YouTube:** [youtube.com/@melgarafael](https://www.youtube.com/@melgarafael)

---

## 📜 Licencia

Distribuido bajo la licencia **MIT** — ver [`LICENSE`](LICENSE). Puedes usar, modificar y
distribuir libremente, incluso con fines comerciales. El software se entrega **"tal cual", sin
garantías** (ver la cláusula de exención en `LICENSE`).

---

## 🛟 Soporte y responsabilidades (self-host)

Este es un proyecto **self-host**: cada persona corre el CRM en su **propia infraestructura**
(VPS, base de datos Supabase y clave de IA propios). Eso implica:

- **El soporte es comunitario y "tal cual".** Las dudas y los bugs entran como
  [Issues](https://github.com/melgarafael/DeskcommCRM/issues) o
  [Discussions](https://github.com/melgarafael/DeskcommCRM/discussions). No hay SLA ni
  soporte garantizado — es open source mantenido por buena voluntad.
- **Eres responsable de tu instalación.** Las actualizaciones no son automáticas (haces clic o
  ejecutas `update.sh` cuando quieras), y mantener y respaldar tu servidor te corresponde a ti.
- **Protección de datos (LGPD) — atención:** quien **hospeda** la instancia es el
  **responsable** (controlador) de los datos personales que ahí se tratan (clientes,
  conversaciones, pedidos), con las obligaciones legales que eso implica. Los mantenedores del
  proyecto **no son** responsables ni encargados de tu instancia, y no tienen acceso a tu base de
  datos, a tu WhatsApp ni a tu storage. Lo único que puede salir de tu máquina hacia nosotros es
  el reporte de errores descrito abajo — y solo si tú lo permites.
- **Telemetría (Sentry):** `install.sh` **pregunta** durante la instalación y respeta tu
  respuesta; en modo no interactivo, sin `SENTRY_DSN` definido, la telemetría queda
  **desactivada**. Si aceptas el Sentry de la comunidad, lo que se envía son **reportes de
  error** (stack trace) con CPF, teléfono y correo sustituidos, cabeceras sensibles eliminadas,
  y el token de webhook/invitación redactado de la URL — **sin** rastreo de rendimiento y **sin**
  replay de sesión, que quedan en 0 en ese camino. Para desactivarla en cualquier momento:
  `SENTRY_DSN=off` en el `.env`. Para enviarla a **tu** Sentry (ahí sí con rendimiento y replay):
  `SENTRY_DSN=<tu-dsn>`. Qué se redacta, y por qué, está en
  [`lib/sentry/scrub.ts`](lib/sentry/scrub.ts); la resolución del DSN en
  [`lib/sentry/dsn.ts`](lib/sentry/dsn.ts).

---

## 🙏 Agradecimientos

- **WAHA** ([devlikeapro](https://waha.devlikeapro.com/)) — motor de WhatsApp.
- **Supabase** — Postgres + Auth + Storage + Realtime en un solo stack.
- **Hostinger** — hosting recomendado para instalar el proyecto, con el cupón `THALENA` para cuentas nuevas en los planes anuales de VPS.
- **Anthropic**, **OpenAI** y **OpenRouter** — los proveedores de IA que el CRM sabe usar.
- **shadcn/ui** — base de componentes.
- La comunidad que nos llevó del e-commerce a clínicas, inmobiliarias, infoproductos y más allá — ustedes definieron lo que es este proyecto.

---

<div align="center">

**Built with ☕ in Brasil** · **Made for the community**

Sigue el desarrollo: [Instagram](https://www.instagram.com/melgarafael) · [YouTube](https://www.youtube.com/@melgarafael)

</div>
