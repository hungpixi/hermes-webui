# ExportMate DDD dashboard MVP pattern

Session pattern from ExportMate MVP continuation.

## Trigger

Use when user says SaaS MVP/dashboard looks fake, lacks charts/numbers/images, or asks to start presenting frontend/backend architecture with DDD.

## Durable approach

1. Keep TailAdmin/custom ExportMate style, but add data visual proof on every generic dashboard-like page:
   - KPI cards are not enough.
   - Add chart/bars/pipeline panels showing operational numbers.
   - Use labels tied to domain entities, not generic analytics.
2. Introduce DDD boundaries without heavy rewrite:
   - shared contracts under `src/shared/domain/<product>/contracts.ts`
   - backend domain service under `src/server/domain/<product>/<service>.ts`
   - API handler imports service and returns presentation-ready domain summaries.
3. Use bounded contexts as presentation language:
   - document-compliance
   - market-intelligence
   - product-catalog
   - shipment-orchestration
   - supplier-network
   - buyer-growth
   - billing-credits
4. For reusable visual proof, create shared components, e.g. `VisualDataPanel`, and inject into `PageTemplate`/`FeatureWorkspace` so many pages improve at once.
5. Add a presentation page when user asks to explain frontend/backend architecture:
   - route: `/architecture`
   - app route wrapper under `src/app/(dashboard)/architecture/page.tsx`
   - domain page under `src/domains/architecture/page.tsx`
   - include layers: frontend domain pages, shared domain contracts, backend domain services, API adapters.
6. Add domain API mock endpoints before generic resource handling, e.g. `GET /api/v1/domain/<bounded-context>/records|actions|metrics`, so the architecture page and demo have backend proof.
7. Add export/share affordances to reusable templates:
   - `downloadCsv(filename, rows)` helper using Blob + temporary anchor
   - `copyShareLink(path)` helper using `navigator.clipboard`
   - buttons in `FeatureWorkspace` and `PageTemplate` with toast feedback.
8. Preserve honest demo status: mark sample panels as demo/live demo data if not real backend data yet.
9. Validate before commit:
   - `npm run lint`
   - `npm run build`
10. If previous commit was active MVP commit and user says continue, amend same commit unless user asks new commit.

## Pitfalls

- Do not stop at commit-message amend when user means “continue MVP implementation”.
- Do not make dashboards only text/table/card; add charts/pipelines/images and concrete numbers.
- Do not over-engineer DDD; add thin contracts/services first, then migrate features incrementally.
- Do not add local reference/template zip folders unless user explicitly wants them tracked.

## Example path set

- `src/shared/domain/exportmate/contracts.ts`
- `src/server/domain/exportmate/dashboard-service.ts`
- `src/shared/components/exportmate/VisualDataPanel.tsx`
- `src/server/api/handlers.ts`
- `src/shared/components/exportmate/PageTemplate.tsx`
- `src/shared/components/exportmate/FeatureWorkspace.tsx`
