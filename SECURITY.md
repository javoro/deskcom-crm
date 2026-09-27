# Política de Seguridad

## Versiones soportadas

DeskcommCRM se distribuye en rolling release a partir de la branch `main`. Las correcciones de seguridad se aplican solo a la versión más reciente — mantén tu instalación actualizada (`bash setup-kit/update.sh` en self-host).

| Versión | Soportada |
| --- | --- |
| `main` (más reciente) | ✅ |
| Snapshots antiguos | ❌ |

## Reportar una vulnerabilidad

**No abras un issue público para vulnerabilidades.**

Usa el [reporte privado de vulnerabilidades de GitHub](https://github.com/melgarafael/DeskcommCRM/security/advisories/new) — el reporte llega solo a los mantenedores, y el historial queda auditable.

Qué esperar:

- **Confirmación de recepción** en un plazo de 7 días.
- **Evaluación y respuesta** en un plazo de 30 días, con un plan de corrección cuando se confirme.
- **Crédito** en el advisory publicado, si así lo quieres.

## Alcance

Interesan especialmente los reportes sobre:

- Fuga entre tenants (bypass de RLS o del filtro `organization_id`)
- Bypass de autenticación/RBAC (roles `viewer`/`agent`/`manager`/`admin`, super-admin)
- Exposición de datos personales (LGPD): contactos, conversaciones, medios de WhatsApp
- Inyección vía payloads de webhook (WAHA, Nuvemshop) o de la API `/api/v1`
- Fuga de secretos (API keys, bearer tokens, cookies de sesión)

Las instalaciones self-host son responsabilidad de quien las hospeda; los problemas de configuración del servidor (firewall, TLS del VPS, etc.) están fuera del alcance del proyecto, pero las mejoras al kit de instalación son bienvenidas como un issue normal.
