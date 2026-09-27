# 🧭 Visión — DeskcommCRM

> **El sistema operativo de ventas con agentes de IA, open source, nativo en WhatsApp.**
> Este documento es la fuente de la verdad del posicionamiento del proyecto. Todo lo que sea público (README, sitio, docs, descripciones) se deriva de aquí.

---

## El nombre

**Deskcomm** viene de **Desk** (escritorio) + **comm** (comercio): **el comercial de escritorio**.
La idea que carga el nombre: toda la operación comercial de un negocio — atención, calificación, embudo, posventa — operada desde un único escritorio, por personas y por agentes de IA trabajando juntos.

El "CRM" en el nombre es la categoría de entrada, no el techo. DeskcommCRM es **más que un CRM**: es el sistema donde sucede la venta.

## De dónde venimos, hacia dónde vamos

El proyecto nació en 2026 como un CRM operativo para **e-commerce brasileño** — WhatsApp vía WAHA, integración con Nuvemshop, LGPD nativa. Cuando abrimos el código, la comunidad decidió otra cosa: la mayoría de quienes lo adoptaron empezó a correr Deskcomm en **clínicas, infoproductos, inmobiliarias, agencias y prestadores de servicios** — cualquier negocio que vende conversando.

Las solicitudes de funciones de esa comunidad empujaron el producto en la dirección que hoy es nuestra identidad: **agentes de IA cada vez más capaces, integrados al sistema vía MCP, operando el CRM de verdad**. El e-commerce sigue siendo un caso de uso de primera clase (fue nuestra cuna y la integración con Nuvemshop lo prueba) — pero es **una** vertical, no **el** producto.

**La transición, en una frase:** de "CRM de e-commerce con IA" a **"sistema operativo de ventas con agentes de IA, para cualquier negocio que vende por WhatsApp"**.

## Lo que creemos sobre los agentes de IA

1. **Un agente que opera, no un chatbot que adorna.** Nuestro agente lee contexto real (historial, perfil, pedido), consulta la base de conocimiento del tenant (RAG por organización), responde, califica, mueve el lead en el embudo — y es **responsable (assignee) de primera clase** en el sistema, con las mismas reglas de gobernanza que un agente humano.

2. **Agentes que se mejoran a sí mismos.** El sistema está diseñado como un flywheel: las conversaciones resueltas se vuelven conocimiento nuevo en la base RAG; los handoffs a una persona marcan dónde el agente todavía no llega; las métricas y el presupuesto por tenant cierran el ciclo. Cada día de operación hace mejor al agente — con **aprobación humana** en las decisiones que importan. Esa es la apuesta central del roadmap.

3. **MCP como sistema nervioso.** El CRM entero se expone como tools MCP — primero para los agentes internos, después como contrato público. Un negocio debe poder conectar el agente que quiera (Claude, el que venga) y que este **opere** Deskcomm: crear un lead, responder a un cliente, agendar, consultar un pedido. El CRM se vuelve infraestructura para agentes.

4. **La persona al mando.** Handoff auditado, alcance por rol (RBAC), cola con posición, presupuesto de IA por organización. La autonomía del agente crece en la medida en que la gobernanza demuestra que acierta.

## Los pilares del producto

| Pilar | Qué significa en la práctica |
|---|---|
| **Agentes de IA nativos** | RAG por tenant, análisis de sentimiento, handoff IA→humano auditado, IA como responsable, presupuesto por organización |
| **CRM automatizado por la IA** | El agente mueve leads, aplica etiquetas, dispara automatizaciones CUANDO/SI/ENTONCES — el embudo avanza solo |
| **Herramientas de apoyo al área comercial** | Inbox en tiempo real, kanban con fractional indexing, customer 360, métricas por agente, enrutamiento automático |
| **Nativo de WhatsApp** | WAHA multinúmero, anti-baneo, medios, detección de STOP — el canal donde se vende en Latinoamérica |
| **Multinicho por diseño** | `vocabulary` configurable por pipeline (lead = Cliente/Paciente/Comprador; won = Pagado/Agendado/Cerrado) — el mismo núcleo sirve para e-commerce, clínicas, inmobiliarias, infoproductos |
| **Self-hosted de verdad** | Tus datos en tu VPS, kit de instalación con 1 comando, `baseline.sql` auto-curativo, actualización con 1 script |
| **Cumplimiento nativo** | Multi-tenant con RLS probada en CI, LGPD por diseño (redact, data_request, anonimización), audit append-only |

## Posicionamiento

**Categoría de entrada (ancla):** la alternativa **open source y self-hosted** a las plataformas cerradas de atención y ventas por WhatsApp (Kommo, Octadesk, Intercom, Zendesk).

**Categoría propia (bandera):** **sistema operativo de ventas con agentes de IA** — *AI Sales OS*. Es hacia donde nos lleva el ancla: los incumbentes venden una suscripción de chat con un bot acoplado; nosotros entregamos un sistema donde el agente de IA es operador nativo y el código es tuyo.

**Una frase (es):**
> DeskcommCRM es el sistema operativo de ventas open source con agentes de IA nativos y WhatsApp — self-hosted, multi-tenant, para cualquier negocio que vende conversando.

**One-liner (en):**
> Open-source AI sales OS: a self-hosted CRM where AI agents natively operate sales and support over WhatsApp — an open alternative to Kommo, Octadesk and Intercom.

**Público:** negocios de Latinoamérica (y más allá) que venden por WhatsApp — e-commerce, clínicas, inmobiliarias, infoproductores, agencias, servicios — y la comunidad dev/self-hosted que lo instala para sí o para sus clientes.

## Modelo del proyecto (sin letra chiquita)

- **El software es 100% open source (MIT), completo, sin versión de pago.** No vendemos suscripción. No existen funciones bloqueadas.
- **La monetización es por infraestructura:** **Hostinger** es el hosting recomendado para el camino self-host, con el cupón `THALENA` para cuentas nuevas en los planes anuales de VPS. El proyecto sigue sin suscripción ni funciones de pago.
- **El camino genérico nunca se sabotea:** `docker compose` y el `setup-kit` funcionan en cualquier VPS. La recomendación nunca es el único camino. (Regla de oro del open source sostenible: la percepción de trampa mata la marca.)

## Principios de comunicación

1. **Keyword primero, jerga después.** En todo título público: "open source", "AI agents", "WhatsApp", "CRM", "self-hosted" antes de cualquier nombre interno de subsistema.
2. **Mostrar, no describir.** Screenshot/GIF del producto en el primer scroll de cualquier página.
3. **Ancla explícita.** "Alternativa open source a X" aparece en el About de GitHub, en el README y en el sitio — es así como nos encuentra la demanda de los incumbentes (buscadores y LLMs).
4. **El e-commerce es un ejemplo, no la definición.** Al citar casos de uso, siempre en lista multinicho ("e-commerce, clínicas, inmobiliarias...").
5. **Transparencia del modelo.** Recomendación de hosting, cupón y telemetría declarados en lenguaje humano en el README, nunca escondidos.

## Norte a 3 años

Ser la respuesta por defecto — de Google, de ChatGPT, de Reddit y del dev latinoamericano — a la pregunta **"¿cuál es el mejor CRM open source con agentes de IA y WhatsApp?"**; con miles de instancias self-hosted corriendo, un ecosistema de agentes conectados vía MCP público, y un flywheel de automejora que haga que cada instancia venda mejor con cada mes de operación.

---

*Última revisión: 2026-07-19 — reposicionamiento e-commerce → multinicho / AI Sales OS. Traducido al español el 2026-09-27.*
