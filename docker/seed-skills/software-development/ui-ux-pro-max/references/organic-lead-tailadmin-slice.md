# Organic Lead Engine TailAdmin Slice

Session pattern for turning TailAdmin into a SaaS dashboard demo quickly.

## Source

- Upstream: `https://github.com/TailAdmin/free-nextjs-admin-dashboard`
- Demo: `https://nextjs-demo.tailadmin.com`
- Free demo: `https://nextjs-free-demo.tailadmin.com`
- Docs: `https://tailadmin.com/docs`

## Local template path used

- Template: `D:/code/frontend-template/free-nextjs-admin-dashboard`
- App copy: `D:/code/organic-lead-dashboard`

## Useful TailAdmin files

- `src/layout/AppSidebar.tsx` — replace nav IA.
- `src/layout/AppHeader.tsx` — keep baseline.
- `src/layout/SidebarWidget.tsx` — replace TailAdmin upsell with product CTA/status.
- `src/app/(admin)/page.tsx` — main overview.
- `src/components/ui/badge/Badge.tsx`
- `src/components/ui/button/Button.tsx`
- `src/components/ui/table/index.tsx`
- `src/components/charts/*`
- `src/components/calendar/Calendar.tsx`

## Organic Lead Engine IA

- Overview
- Prospects
- Audit Reports
- Keyword Clusters
- Content Calendar
- Outreach Queue
- Automations
- Settings
- UI Kit

## First slice pages

- `/` overview dashboard
- `/prospects`
- `/audit-reports`
- `/automations`

## Verification

```bash
cd D:/code/organic-lead-dashboard
npm install
npm run build
```

Known result in session:

- build OK
- 24 static routes generated
- `npm install` reported vulnerabilities from dependency tree; do not run `npm audit fix --force` without explicit approval.

## User reporting preference

For this user, generate plan/report artifacts as HTML files, not only Markdown. Include visual sections/cards, file paths, build status, known issues, and next steps.
