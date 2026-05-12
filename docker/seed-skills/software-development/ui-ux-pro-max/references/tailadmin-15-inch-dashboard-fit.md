# TailAdmin 15.6-inch dashboard fit pattern

Use when adapting TailAdmin-style SaaS/admin dashboards for typical 15.6-inch laptop screens (~1366–1536px wide, ~768–864px tall), especially when user says layout feels cramped or wants features arranged like a reference folder.

## Pattern

- Treat sidebar IA first, not individual cards. Group by user job:
  - Core operation/actions
  - Master data/profiles
  - Growth/business development
  - System/account
- Add visible sidebar group labels when regrouping; otherwise ordering changes are invisible.
- Reduce chrome before removing product content:
  - Sidebar width `240px` → about `228px`
  - Header height `64px` → about `58px`
  - Main padding `12px` → about `10px` on 15-inch, restore slightly at `2xl`
  - Menu item height `34px` → about `31px`
- Compact persistent promo/plan widgets by trimming vertical margins/padding before hiding them.
- Prefer `clamp()` sizes and `max-w-screen-2xl` content wrappers for dashboard pages.
- Keep TailAdmin component grammar: cards, KPI rows, tables, filters, badges, right-side detail panels.

## Page-level compaction after functional MVP wiring

When the user says specific pages are too tall on a 15.6-inch viewport, compact the local page sections before deleting features:

- Measure current viewport and scroll height in browser console:
  - `({path:location.pathname,w:innerWidth,h:innerHeight,scrollH:document.documentElement.scrollHeight,scrollW:document.documentElement.scrollWidth})`
- Target high-impact vertical sources first:
  - hero/header copy: reduce padding, heading size, paragraph margin/line-height
  - KPI cards: reduce `min-height`/fixed `h-*`, icon size, value margin, card padding
  - grid sections: reduce `gap-*`, `space-y-*`, bottom padding
  - large artificial min-heights: lower page/table `min-h-*` when content can still scroll internally
  - action buttons: reduce height/padding on dense headers
- Keep features visible and responsive; avoid hiding panels unless the user asks for a minimal mode.
- If a compact pass exposes a dead header action, wire it while there (example: share button -> toast/link response).

Session-proven ExportMate compaction examples:

- Dashboard: compact hero `p-4 -> p-3`, smaller heading/paragraph, KPI `min-h` about `5.25rem -> 4.5rem`, smaller inter-card gaps.
- Supplier profiles: reduce KPI card padding/gaps and page `space-y`/bottom padding before changing table/content structure.
- Shipment passports: reduce header/action button height, main gaps, table-area min-height (`700px -> 620px`), KPI card height (`136px -> 104px`), icon/value spacing.

## Verification

- Run lint and production build after layout changes.
- If dependencies are missing but lockfile exists, run `npm install`, then `npm run lint`, then `npm run build`.
- Do not run `npm audit fix --force` without explicit approval; it can break Next/TailAdmin dependency tree.

## Example session paths

- `src/shared/config/navigation.ts`: regroup nav into core operation vs profiles/growth/system.
- `src/shared/components/layout/ExportMateShell.tsx`: compact sidebar/header/main chrome for 15.6-inch laptop fit.
