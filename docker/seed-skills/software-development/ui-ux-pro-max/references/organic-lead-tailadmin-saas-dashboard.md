# Organic Lead Engine TailAdmin SaaS Dashboard Reference

Session-derived reusable pattern for this user's SaaS dashboard work.

## User-specific UI rule

- Use `ui-ux-pro-max` for UI/UX work.
- Use TailAdmin for SaaS dashboards.
- Treat the approved/shared TailAdmin page as canonical scientific data/function presentation.
- Avoid heavy reformatting once the user approves the layout; evolve by adding data, routes, states, and product-specific copy.
- Multi-step plans/reports should be delivered as HTML files plus short chat paths/URLs.

## TailAdmin SaaS shell checklist

Start with `TailAdmin/free-nextjs-admin-dashboard`, then add:

- Main dashboard overview with metrics/cards/tables.
- Domain nav: Overview, Prospects, Audit Reports, Keyword Clusters, Content Calendar, Outreach Queue, Reports, Automations, Settings.
- Settings page with language switch shell and agent/provider settings.
- Auth flow: `/signin`, `/signup`, `/reset-password` with branded auth side panel.
- Shared data module (`src/lib/...`) feeding pages and `/api/dashboard`.
- Small API endpoints for demo/preview workflows (`/api/audit`, `/api/dashboard`).
- HTML implementation report under `reports/`.

## Branding pattern

For Organic Lead Engine/AI agent SaaS:

- Blue/cobalt/azure palette.
- Logo symbolism: AI agent, growth arrow/sprout, search/orbit, neural nodes, data pipeline.
- Keep mark simple enough for sidebar/avatar/favicon; pair with a clean wordmark.
- Replace TailAdmin logo/profile lightly; don't disrupt spacing or component hierarchy.

## 9Router image generation pattern

Use `ai-gateway-9router` + upstream `9router-image` pattern:

- Discover image models: `GET $NINEROUTER_URL/v1/models/image`.
- Generate via `POST $NINEROUTER_URL/v1/images/generations`.
- Good prompt shape: professional SaaS logo icon, no text, transparent background if possible, color palette, symbolic layers, small-size usability.
- Save under `public/images/logo/...` and verify with visual analysis.

## Verification checklist

- `npm run build` must pass before deploy.
- Verify key routes after deploy: `/`, `/settings`, `/signin`, `/signup`, `/reset-password`, feature pages, `/api/dashboard`.
- Do not print secrets when setting envs or verifying APIs.

## Known good route set

```txt
/
/prospects
/audit-reports
/keyword-clusters
/content-calendar
/outreach-queue
/reports
/automations
/settings
/signin
/signup
/reset-password
/api/dashboard
/api/audit
```
