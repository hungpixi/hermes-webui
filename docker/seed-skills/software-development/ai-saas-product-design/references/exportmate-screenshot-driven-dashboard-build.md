# ExportMate screenshot-driven dashboard build lessons

Use when user provides a folder of dashboard screenshots and says to code features/pages from them.

## Workflow

1. Treat screenshots as product specs, not only visual references.
2. First inventory image files, dimensions, and route mapping (`1_dashboard.png` -> `/dashboard`, etc.).
3. Use vision pass per screenshot or batch to extract:
   - page purpose
   - KPI cards
   - filters/search/dropdowns
   - main table columns/rows/actions
   - right-side AI panel/drawer/forms
   - empty/loading/error states
   - top CTA buttons
   - human-approval gates
4. Implement in tiers:
   - Tier 1: data/content parity for all pages (titles, KPIs, table rows, AI right panel)
   - Tier 2: shared interactions (search, status filter, reset, export CSV, share link, toast/backend action)
   - Tier 3: bespoke layouts for high-value pages (dashboard, supplier/product profiles, shipment passports, market requirements, evidence vault, document checks)
   - Tier 4: pixel-perfect screenshot matching in small batches (3 pages per pass)
5. Run page smoke tests via HTTP status and at least a few browser navigations/clicks.
6. Run `npm run lint && npm run build` before claiming done.

## Patterns that worked

- Keep one robust shared page component (`PageTemplate`) for secondary pages, but add enough generic interactions: search input, status select, reset filters, `Share link`, `Export CSV`, and action buttons calling backend.
- For demo DDD, separate:
  - `src/shared/domain/.../contracts.ts`
  - `src/server/domain/.../*-service.ts`
  - `src/server/api/*` as adapters
  - `src/domains/*` as frontend bounded-context UI
- Add `/architecture` page to explain frontend/backend separation for presentation.
- Demo API endpoints should work without custom headers when intended for browser/demo use; fallback to `demo-company` is acceptable for MVP mock APIs.
- Clipboard share links need a fallback when `navigator.clipboard` is blocked by browser permissions.

## Pitfalls

- Do not say “finished/pixel-perfect” after only data parity. If only shared template/data was updated, call it “bám ref hơn” and recommend per-batch bespoke matching.
- Do not ignore untracked reference assets; leave them uncommitted if user only uses them as inputs.
- If a dev server launch fails because wrong cwd or stale lock, verify actual server with `curl http://localhost:3000/<route>` before spending time on transient process warnings.
- For screenshot-driven work, prefer concrete UI content from screenshots over generic placeholder SaaS copy.

## Quick verification snippets

```bash
npm run lint && npm run build

for p in dashboard architecture market-requirements product-profiles shipment-passports supplier-profiles document-checks cost-map export-tasks; do
  curl -s -o /tmp/$p.html -w "$p %{http_code} %{size_download}\n" http://localhost:3000/$p
done

for a in dashboard/summary domain/market-intelligence/records domain/product-catalog/records domain/shipment-orchestration/records; do
  curl -s -o /tmp/api.json -w "api $a %{http_code} %{size_download}\n" http://localhost:3000/api/v1/$a
done
```
