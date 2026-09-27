---
type: index
project: DeskcommCRM
status: draft
last_updated: 2026-07-29
generated_by: auditoria documental (Claude Code)
confidence: alta (el inventario de archivos es CONFIRMADO; la agrupación temática es INFERIDA)
audited_against: origin/main @ 789dfa6 (v1.0.0, 2026-07-27)
---

# Índice de la documentación — DeskcommCRM

Mapa de los **154** archivos `.md` de `docs/`, repartidos en **20** subcarpetas — medido el
2026-08-14, con las reglas al lado: `git ls-files 'docs/**/*.md' | wc -l` y
`git ls-files 'docs/**/*.md' | sed 's|^docs/||;s|/.*||' | sort -u | wc -l`. Los dos números
estaban equivocados (149 y 24) y la segunda regla ni siquiera existía — es la misma clase que
cataloga [`audits/2026-08-14-afirmacoes-de-estado.md`](audits/2026-08-14-afirmacoes-de-estado.md).
Existe porque la documentación creció sin punto
de entrada: sin este índice, personas y agentes no encuentran lo que ya se decidió y
reescriben encima.

> **Idioma.** Este índice y los documentos de la raíz (`README.md`, `CLAUDE.md`, `AGENTS.md`,
> `VISION.md`, `ARCHITECTURE.md`, `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`) están
> en español. Los documentos enlazados dentro de `docs/` siguen en portugués de Brasil, el idioma
> del proyecto original, salvo que se indique otra cosa. Los nombres de archivo no se tradujeron:
> son rutas, y cambiarlas rompería enlaces y tests.

**Regla de precedencia cuando dos docs no coinciden:**
`CLAUDE.md` (doctrina) > `docs/specs/` (contrato técnico) > `docs/prd/` (intención) >
`HANDOFF-*.md` (estado de sesión) > README. Si encontraste una divergencia, corrige la fuente
de menor precedencia y regístralo.

---

## 1. Empieza aquí

| Doc | Para qué |
|---|---|
| [`README.md`](../README.md) | Qué es, quickstart de 5 min, stack, roadmap. También en [EN](../README.en.md) / [PT-BR](../README.pt-BR.md) |
| [`VISION.md`](../VISION.md) | Posicionamiento, por qué self-host, para quién |
| [`ARCHITECTURE.md`](../ARCHITECTURE.md) | Arquitectura en 1 página |
| [`AGENTS.md`](../AGENTS.md) | Contrato para agentes de código (cualquier herramienta) |
| [`CLAUDE.md`](../CLAUDE.md) | **Doctrina no negociable.** Convenciones, anti-patterns, Definition of Done |
| [`CONTRIBUTING.md`](../CONTRIBUTING.md) | Cómo contribuir |
| [`CHANGELOG.md`](../CHANGELOG.md) | Cambios por versión (SemVer). **Quien opera un VPS lo lee antes de `update.sh`** — un cambio que exige acción manual aparece bajo "⚠️ Requer atenção" |
| [`docs/current-state.md`](current-state.md) | **Qué está listo, incompleto y roto hoy** |

## 2. Producto e intención

| Doc | Contenido |
|---|---|
| [`prd/00-prd-master.md`](prd/00-prd-master.md) | PRD maestro — visión, alcance del MVP, KPIs, restricciones |
| [`prd/01-prd-platform-base.md`](prd/01-prd-platform-base.md) | Auth, tenancy, RBAC, framework LGPD |
| [`prd/02-prd-customer-360.md`](prd/02-prd-customer-360.md) | Customer 360 + resolución de identidad determinística |
| [`prd/03-prd-whatsapp-waha.md`](prd/03-prd-whatsapp-waha.md) | Canal WhatsApp, anti-baneo, ventana de 24h |
| [`prd/04-prd-pipeline-attendance.md`](prd/04-prd-pipeline-attendance.md) | Kanban, atención, tickets, handoff |
| [`prd/05-prd-ai-rag-handoff.md`](prd/05-prd-ai-rag-handoff.md) | IA conversacional, RAG por tenant, sentimiento |
| [`prd/06-prd-nuvemshop-lgpd.md`](prd/06-prd-nuvemshop-lgpd.md) | Integración Nuvemshop + webhooks LGPD |
| [`business-rules/00-business-rules-catalog.md`](business-rules/00-business-rules-catalog.md) | **Catálogo de reglas de negocio** — fuente de la verdad fuera del código |
| [`presentation/pitch-deck.md`](presentation/pitch-deck.md) | Pitch |

## 3. Contrato técnico (specs)

Detallan el schema SQL y los payloads exactos. **Consúltalas antes de modelar cualquier cosa.**

| Spec | Dominio |
|---|---|
| [`specs/01`](specs/01-spec-platform-base.md) | Plataforma base — tenancy, RLS, RBAC, API, audit |
| [`specs/02`](specs/02-spec-customer-360.md) | Customer 360 |
| [`specs/03`](specs/03-spec-whatsapp-waha.md) | WAHA — cola outbound, warm-up, spinning, crons |
| [`specs/04`](specs/04-spec-pipeline-attendance.md) | Pipeline y atención |
| [`specs/05`](specs/05-spec-ai-rag-handoff.md) | IA, RAG, disparadores de handoff |
| [`specs/06`](specs/06-spec-nuvemshop-lgpd.md) | Nuvemshop + LGPD |
| [`specs/07`](specs/07-spec-events-workers.md) | **`event_log`, workers, claim atómico, backoff/DLQ** |
| [`specs/08`](specs/08-spec-deploy-observability.md) | Deploy y observabilidad |
| [`specs/09`](specs/09-spec-frontend-backend-integration.md) | Integración front/back |
| [`specs/10`](specs/10-spec-ai-agents-runtime.md) | Runtime de los AI Agents |
| [`specs/11`](specs/11-spec-mcp-server-internal.md) | Servidor MCP interno + catálogo de tools |
| [`specs/12`](specs/12-spec-ai-agents-ui.md) | UI de los AI Agents |
| [`specs/13`](specs/13-spec-governanca-atendimento.md) | Gobernanza de atención (épica G1–G6) |
| [`specs/14`](specs/14-contrato-governanca-agentes-externos.md) | Contrato para agentes de IA externos |
| [`specs/15`](specs/15-spec-casos-humanos.md) | Casos humanos (la IA delega a una persona) |
| [`specs/16`](specs/16-spec-tres-papeis-do-agente.md) | **Tres roles del agente** — Conversador / Operador / Seguridad |
| [`specs/17`](specs/17-spec-conversa-vira-lead.md) | **La conversación se vuelve lead** — el eslabón entre atención y CRM |
| [`specs/17`](specs/17-spec-indice-de-atrito.md) | **Índice de Fricción** — medir el propósito (menor fricción para ambos lados), no la actividad |
| [`specs/RECONCILIATION-LOG.md`](specs/RECONCILIATION-LOG.md) | Log de reconciliación entre specs |

## 4. Doctrina y arquitectura

| Doc | Contenido |
|---|---|
| [`doctrine/sistema-vivo.md`](doctrine/sistema-vivo.md) | **Doctrina del Sistema Vivo — la LEY.** 7 invariantes + regla del tiempo + Living System Checklist (ítem 13 del DoD) |
| [`doctrine/sistema-vivo/`](doctrine/sistema-vivo/README.md) | **Manual del Sistema Vivo** — 8 capítulos enchufables (principio universal + aplicación de referencia). El *porqué* de cada invariante, y cómo adoptar la doctrina en otro sistema |
| [`doctrine/restricao-de-canal.md`](doctrine/restricao-de-canal.md) | Auto-restricción × hetero-restricción de canales externos; contrato de parámetros derivado |
| [`doctrine/separacao-fala-e-operacao.md`](doctrine/separacao-fala-e-operacao.md) | El vocabulario interno nunca se filtra al cliente |
| [`doctrine/packaging.md`](doctrine/packaging.md) | **Doctrina de Packaging — la LEY.** 8 invariantes + política de canales + checklist de release (ítem 15 del DoD) |
| [`adr/0001-packaging-e-distribuicao.md`](adr/0001-packaging-e-distribuicao.md) | ADR del packaging: namespace, los 3 packages, y lo que se rechazó |
| [`architecture/agent-turn.html`](architecture/agent-turn.html) | Diagrama del turno del agente (inbound → guardrails → outbound) |
| [`specs/pre-go-live-whatsapp.md`](specs/pre-go-live-whatsapp.md) | Modo de prueba de WhatsApp por canal: lista de teléfonos, apertura al público y compatibilidad con la autorización por origen |
| [`architecture/pre-go-live-whatsapp.architecture.json`](architecture/pre-go-live-whatsapp.architecture.json) | Mapa del pre-go-live, configuración administrativa y gate compartido |
| [`architecture/teto-de-orcamento.architecture.json`](architecture/teto-de-orcamento.architecture.json) | **Mapa vivo del tope de gasto en IA** — quién alimenta el gate, qué NO deshace sola la detención, y el lazo de retorno (invariante 7) |
| [`release/teto-de-orcamento.md`](release/teto-de-orcamento.md) | **Nota de release para quien opera un VPS** — qué cambia, qué hacer (nada), el cambio de etiqueta de R$ a US$ y cómo activar la protección |
| [`research/architecture-diagrams.md`](research/architecture-diagrams.md) | Diagramas de arquitectura |
| [`research/reference-synthesis.md`](research/reference-synthesis.md) | Arquitectura heredada de la referencia de WAHA |
| [`research/followup-reference-mining.md`](research/followup-reference-mining.md) | Investigación del motor de follow-up |
| [`threat-model.md`](threat-model.md) | **Superficie de ataque real del self-host** |
| [`alertas-de-seguranca-triados.md`](alertas-de-seguranca-triados.md) | La razón de cada alerta **descartada** en el panel de GitHub, y lo que la revisión por clase encontró que el scanner no ve |

## 5. Design system

[`design-system/README.md`](design-system/README.md) es el punto de entrada (v1.0, 5 elecciones
visuales fijadas: paleta Sage, Atkinson Hyperlegible, densidad aireada, Phosphor duotone,
IBM Plex Mono). Numerados `00`–`09`: overview, tokens, paleta, tipografía, densidad,
iconografía, componentes, motion, voice & tone, **anti-patterns**.
Flujo de pantallas en `design-system/screen-flow/` (recorridos, clickflows, máquinas de estado,
accesibilidad).

## 6. Operar e instalar

| Doc | Contenido |
|---|---|
| [`SETUP.md`](SETUP.md) | Guía completa de env vars y setup local |
| [`deploy-selfhost/README.md`](deploy-selfhost/README.md) | Self-host genérico |
| [`deploy-vps/README.md`](deploy-vps/README.md) | VPS Hostinger (`install.sh`, `backup.sh`, `reset-mfa.sh`) |
| [`DEPLOY-CHECKLIST.md`](DEPLOY-CHECKLIST.md) | Checklist de deploy |
| [`ATUALIZANDO.md`](ATUALIZANDO.md) | `update.sh`, `restore.sh`, `healthcheck.sh` |
| [`runbooks/deploy.md`](runbooks/deploy.md) | **Deploy en producción — los dos `-f` del compose, verificación post-deploy** |
| [`runbooks/remediar-worker-congelado.md`](runbooks/remediar-worker-congelado.md) | **Incidente: el worker congelado** — diagnóstico (`diagnostico.sh`), impacto medido y las dos rutas de remediación. **Todavía no ensayado** |
| [`runbooks/ativar-packaging.md`](runbooks/ativar-packaging.md) | **Activación de la doctrina de packaging** — los 3 pasos que no caben en un PR (paquete público, check obligatorio, primera release) |
| [`runbooks/custo-e-cota-do-supabase.md`](runbooks/custo-e-cota-do-supabase.md) | **"Mi Supabase se pasó de la cuota"** — cómo medir el origen del consumo, los dos intervalos de la cola y las dos tablas que solo crecen |
| [`runbooks/waha-vps.md`](runbooks/waha-vps.md) | Runbook de WAHA en producción |
| [`runbooks/cloudpanel.md`](runbooks/cloudpanel.md) | **VPS que ya tiene CloudPanel/Nginx en los puertos 80/443** — el modo proxy externo del kit, la dirección fija para el Nginx del host y el 403 del webhook global |
| [`runbooks/ai-credentials-rotation.md`](runbooks/ai-credentials-rotation.md) | Rotación de credenciales de IA |
| [`../SECURITY.md`](../SECURITY.md) | Política de reporte de vulnerabilidades |

## 7. Tests y QA

| Doc | Contenido |
|---|---|
| [`testing/user-journey-map.md`](testing/user-journey-map.md) | **Mapa de recorridos vivo** — casos, prioridad `[P0]`, hallazgos. Actualizar siempre |
| [`testing/HANDOFF-vps-qa.md`](testing/HANDOFF-vps-qa.md) | Receta del entorno fresco estilo VPS |
| [`harness-audit.md`](harness-audit.md) | **Auditoría del harness** — 20 ítems + nivel de madurez |
| [`audits/2026-08-14-afirmacoes-de-estado.md`](audits/2026-08-14-afirmacoes-de-estado.md) | **393 afirmaciones de estado medidas contra la fuente**, cada una con el comando que la responde. Es el retrato fechado que sostiene las correcciones de doctrina de esa fecha — revisa la fecha antes de citar cualquier número de ahí |
| [`audits/2026-08-14-alinhamento-stable-v1.3.0.md`](audits/2026-08-14-alinhamento-stable-v1.3.0.md) | Lo que de verdad contiene el tag `v1.3.0` — que es el kit que corre en el VPS, y no `main` |
| [`../tests/e2e/README.md`](../tests/e2e/README.md) | Cómo correr los E2E |

## 8. Ejecución — planes, épicas, handoffs

Documentación de *proceso*. Alta rotación; trátala como estado, no como contrato.

**Convención observada:** una épica **viva** mantiene su HANDOFF en la **raíz** del repo; una épica
**cerrada** se archiva en [`handoffs/`](handoffs/). Úsalo para saber qué está en curso.

- **Raíz (en curso):** `HANDOFF.md` (follow-up), `HANDOFF-harness-evolution.md`, `HANDOFF-operacao-visivel.md`
- [`handoffs/`](handoffs/) — archivados: casos humanos, inbox multimodal, CRM vivo, LGPD, wave1-devvivo, contrato wave5, briefing CRM vivo
- [`stories/`](stories/) — épicas y stories (`epics/MASTER.md` = plan por epic/wave)
- [`superpowers/`](superpowers/) — `plans/` y `specs/` fechados por ola, más `handoffs/`
- [`growth/`](growth/) — material de crecimiento · [`brand/`](brand/) — marca · [`white-label.md`](white-label.md) — instalación con marca propia, también en [en](white-label.en.md) y [es](white-label.es.md) (traducciones selladas por el hash del original; ver `scripts/selar-traducao.ts`)
- [`../plan/`](../plan/) — backlog del gov-loop (`features.json` 31/31, `phases.md`, `progress.md`)
- [`../loop/`](../loop/) — máquina del gov-loop (`LOOP.md`, `CHECKPOINT.md`, `checkpoints/G1..G6-report.md` + `.approved`)
- [`../tasks/todo.md`](../tasks/todo.md) — workflow de construcción original (Fase 0 → PRD → specs)

## 9. Grafo de conocimiento

`graphify-out/` — grafo del repositorio (7310 nodos, 17705 aristas, 538 comunidades en la última
generación). Consúltalo vía el skill `graphify` antes de recorrer el código en bruto. `GRAPH_REPORT.md` trae
god nodes, hyperedges y comunidades. **Generado — no editar.** ⚠️ Se generó contra un árbol
anterior a la v1.0.0; regenéralo (`/graphify .`) antes de confiar en el detalle fino.

---

## Huecos conocidos de este índice

- `docs/vendaval-fusion-plan.md` y `docs/vendaval-vps-deploy-comandos.md` se refieren a una
  integración ("Vendaval") cuyo estado está **POR CONFIRMAR** — el README **ya no la lista** en
  "Próximo", aunque el disparador (`loop/checkpoints/G6.approved`) existe.
- `docs/diagrams/` no tiene `.md` y no se inventarió. `docs/evidence/` es evidencia visual
  (18 PNGs), no documentación de lectura.
- `docs/architecture/` reúne mapas JSON y sus renders disponibles. Consulta el
  [catálogo de mapas](architecture/README.md) y los archivos del directorio; el conteo
  cambia con las entregas. La doctrina exige representar las piezas nuevas y sus relaciones,
  y `tests/unit/mapas-de-arquitetura.test.ts` verifica la forma y los kinds del runtime.
  Ese gate no comprueba, por sí solo, que toda funcionalidad tenga un mapa.
- `docs/growth/` (3 docs) y `docs/brand/` (1) no se leyeron en detalle — clasificados por
  nombre de carpeta, por lo tanto **INFERIDO**.

- [Acompañamiento administrativo por sesión](support-sessions.md) — autoridad, solo lectura, salida y contratos OAuth.
