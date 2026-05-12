# ExportMate DDD demo hardening notes

Use when finishing an AI SaaS MVP that must look demo-ready and be explainable as frontend/backend architecture.

## Durable lessons

- If dashboard pages feel fake, add visible quantitative panels, bar/pipeline charts, and domain-specific KPI copy before adding more empty pages.
- Make a dedicated `/architecture` route when user needs to present DDD: show frontend domain pages, shared contracts, backend domain services, and API adapter boundaries.
- Keep contracts in `src/shared/domain/<product>/contracts.ts`; keep use-case/mock business logic in `src/server/domain/<product>/*`; keep `src/server/api/*` as HTTP adapters.
- For demo APIs, allow a safe fallback company/tenant such as `demo-company`; browser/manual `curl` checks should not fail only because `x-company-id` header is absent.
- Smoke-test both pages and APIs. Page `200` is not enough if advertised demo endpoints return `400`.
- Export/share buttons should give visible feedback: CSV download, toast, and clipboard fallback when `navigator.clipboard` is blocked.
- After running browser-console experiments, check repo root for accidental files created from malformed shell snippets and delete them before commit.

## Suggested smoke test set

```bash
# pages
for p in dashboard architecture market-requirements product-profiles shipment-passports supplier-profiles document-checks cost-map export-tasks; do
  curl -s -o /tmp/$p.html -w "$p %{http_code} %{size_download}\n" http://localhost:3000/$p
done

# APIs without custom headers
for a in dashboard/summary domain/market-intelligence/records domain/product-catalog/records domain/shipment-orchestration/records domain/product-catalog/metrics; do
  curl -s -o /tmp/api.json -w "$a %{http_code} %{size_download}\n" http://localhost:3000/api/v1/$a
done
```

## Commit hygiene

- Run `npm run lint && npm run build` after hotfixes.
- If amending existing MVP commit, verify `git status --branch --short` and do not stage reference zips/reports unless user asked.
