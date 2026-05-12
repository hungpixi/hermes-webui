# TailAdmin AI Agent SaaS Dashboard Pattern

Session-derived pattern for turning TailAdmin into an AI-agent SaaS dashboard without heavy reformatting.

## User-facing rule

When user says shared/TailAdmin page is the scientific standard, preserve the layout system. Change IA, copy, data models, and components inside existing TailAdmin cards/tables/navigation rather than redesigning everything.

## Recommended IA for an AI SEO/lead SaaS

- Overview
- Plan
- Research
- Skill Library
- Prospects
- Audit Reports
- Keyword Clusters
- Content Calendar
- Outreach Queue
- Reports
- Automations
- Settings

## Completeness checklist

- Auth pages branded but still template-consistent:
  - `/signin`
  - `/signup`
  - `/reset-password`
- Settings page includes language preference shell and AI agent/provider profiles.
- Interactive runners on task pages (`/plan`, `/research`) call server APIs, not only client mock output.
- Reports should have JSON and HTML exports for user legibility.
- Keep provider internals generic in UI; secrets and route names stay server/admin-only unless user asks.

## Visual polish

- Blue AI-agent logo can replace TailAdmin logo/profile lightly.
- Preserve spacing, border radius, cards, and nav from TailAdmin.
- Avoid adding overly custom art direction that makes data pages less readable.

## Pitfalls

- Leaving nav items pointing to old template routes (`/calendar`, `/profile`, `/basic-tables`) after creating product-specific pages.
- Making a pretty demo shell but missing auth, settings, language, exports, and report flow.
- Storing reports only as transient UI text; add export endpoints early.
