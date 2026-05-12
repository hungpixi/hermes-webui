---
name: ui-ux-pro-max
description: UI/UX Pro Max workflow for premium landing pages, SaaS dashboards, and product UI. Use TailAdmin as default SaaS dashboard baseline.
---

# UI UX Pro Max

Use for UI/UX work, especially landing pages, SaaS dashboards, product apps, admin panels, and conversion-focused redesigns.

## Defaults

- For SaaS dashboards, use TailAdmin free Next.js dashboard as first baseline.
- Source: `https://github.com/TailAdmin/free-nextjs-admin-dashboard`
- Demo/visual target: `https://nextjs-demo.tailadmin.com`
- Use template reuse + targeted customization over bespoke rebuild when speed matters.

## Workflow

1. Define product goal, primary user, and main job-to-be-done.
2. Separate marketing landing from app/dashboard IA.
3. Choose baseline:
   - SaaS dashboard: TailAdmin
   - Landing: Stripe/Linear/Vercel/Mercury taste anchors, not clones
4. Build IA first:
   - nav/sidebar
   - core entities
   - key screens
   - empty states
   - CTA/action hierarchy
5. Build component grammar:
   - cards
   - stats
   - tables
   - filters
   - charts
   - forms
   - modals
6. Add realistic data and product proof.
7. Validate with responsive, loading, error, and empty states.
8. For this user, write implementation plans/reports as HTML files when plan/report output is needed; chat summary can stay short with paths/URLs.

## TailAdmin Dashboard Rules

Use TailAdmin when user asks for dashboard/admin/SaaS UI.

For AI-agent SaaS dashboards built on TailAdmin, preserve TailAdmin/shared-page structure and expand the product inside it. Use `references/tailadmin-ai-agent-dashboard.md` for IA, auth/settings/report/export completeness, and pitfalls.

For this user, treat an approved/shared TailAdmin page as the canonical data/function presentation baseline: continue implementation within that structure, replace data/features incrementally, and avoid heavy reformatting unless explicitly requested. Preserve TailAdmin information architecture, spacing, tables, cards, and scientific dashboard presentation as much as possible.

Reference pattern: `references/organic-lead-tailadmin-saas-dashboard.md` captures a reusable TailAdmin SaaS shell checklist, branding pattern, 9Router logo generation flow, and verification routes from the Organic Lead Engine dashboard build.

SEO agent capability pattern: `references/seo-agent-capability-dashboard.md` shows how to load SEO skills into `/plan`, `/research`, `/seo-skills`, and `/api/dashboard` without disrupting the approved TailAdmin structure.

SEO skill sync/runner pattern: `references/seo-skill-sync-agent-dashboard.md` shows how to add interactive `/plan` and `/research` runners plus a cron-safe sync script that regenerates dashboard skill metadata from local Hermes `skills/seo`.

15.6-inch dashboard fit pattern: `references/tailadmin-15-inch-dashboard-fit.md` covers compact sidebar/header/main chrome, visible IA groups, and verification when adapting TailAdmin dashboards to laptop screens.

Interactive MVP + Cost Map pattern: `references/interactive-saas-dashboard-mvp.md` covers wiring dashboard/header CTAs, honest status reporting, browser click/screenshot QA, and React Flow/`@xyflow/react` node-map screens with inspector panels.

ExportMate DDD dashboard MVP pattern: `references/exportmate-ddd-dashboard-mvp.md` covers adding visual data panels/charts to avoid fake-looking dashboards, introducing thin DDD shared contracts + server domain services, and amending active MVP commits after lint/build.

Functional MVP page batching pattern: `references/functional-mvp-page-batching.md` covers using reference screenshots as feature inventory, wiring click responses/search/filter/pagination/drawers/export/share/AI toasts, and shipping 3 custom pages per batch for this user.

ExportMate AI-agent dashboard pattern: `references/exportmate-ai-agent-dashboard.md` covers adding an AI Agent control strip, dynamic filters/counts, idempotent mock action endpoints, toast feedback, and 15.6-inch compact dashboard validation.

ExportMate open trade-document agent pattern: `references/exportmate-open-trade-document-agent.md` covers mining BA docs for GitHub/open-schema refs, building a `/trade-documents` template agent cockpit, `generate-from-template` API, source refs, approval guardrails, and tests.

Agent report/export + sellability loop pattern: `references/agent-run-reports-and-sellability-loop.md` covers `/api/agent/run`, JSON/HTML report exports, 9Router server-side fallback handling, auth landing polish, and bounded cron improvement loops toward a strict sellability score.

Pricing/compute-mode pattern: `references/organic-lead-pricing-compute-modes.md` covers Vietnamese SaaS packaging, Hosted AI vs Custom Router/BYOK, Free-plan anti-abuse, credits/limits, and private 9Router/setup revenue model.

Expected screens for Organic Lead Engine:

- `https://github.com/TailAdmin/free-nextjs-admin-dashboard`
- Demo/visual target: `https://nextjs-demo.tailadmin.com`
- Free demo: `https://nextjs-free-demo.tailadmin.com`
- Docs: `https://tailadmin.com/docs`

Session-proven stack as of 2026-05: Next.js 16, React 19, TypeScript, Tailwind CSS v4.

Fast implementation path:

1. Clone/copy TailAdmin into a new app folder rather than rebuilding from scratch.
2. Replace package name, sidebar IA, hero/overview page, and key entity pages first.
3. Keep original utility pages (calendar, charts, tables, UI kit) as speed scaffolding until custom pages exist.
4. Run `npm install && npm run build`; do not run `npm audit fix --force` without explicit approval because it can break the Next/TailAdmin dependency tree.

Expected screens for Organic Lead Engine:

- Dashboard overview
- Leads/prospects table
- Audit reports
- Keyword clusters
- Content calendar
- Outreach queue
- Settings/API providers

Quality bar:

- Clean sidebar + topbar
- KPI cards
- Chart cards
- Data tables with filters
- Status badges
- Recent activity
- Clear primary action per page
- Mobile responsive

## Organic Lead Engine UI Direction

Brand feel:

- AI-native B2B SaaS
- calm, high-trust, data-first
- not crypto/noisy
- dashboard proof beats vague hero copy

Dashboard modules:

- `Lead Engine Score`
- `Prospects Found`
- `Audits Generated`
- `Content Opportunities`
- `Outreach Queue`
- `Revenue/Lead Potential`

Landing proof sections:

- live product dashboard mockup
- sample audit report
- workflow: URL → audit → prospects → content plan → outreach
- pricing: free mini audit, pilot, monthly publishing

## References

- `references/organic-lead-tailadmin-slice.md` — session-proven TailAdmin adaptation pattern for Organic Lead Engine, including paths, IA, first-slice pages, and build/audit notes.
- `references/organic-lead-agent-dashboard-delivery.md` — session-proven end-to-end delivery pattern: 9Router logo generation, TailAdmin brand polish, dashboard data/API layer, audit CLI/API, prospect cronjob, Dokploy deploy/verify.

## Pitfalls

- Do not mention internal 9Router/ds2api/cx in customer-facing copy.
- Do not clone TailAdmin branding; adapt structure/component grammar.
- Avoid fake metrics unless clearly demo/sample.
- Avoid generic AI copy; show exact workflow and outputs.
