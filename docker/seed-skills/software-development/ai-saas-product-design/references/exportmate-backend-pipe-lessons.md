# ExportMate backend-pipe MVP lessons

Use for domain SaaS MVP hardening when UI exists but buttons are still cosmetic.

## Trigger

User says buttons/header/pages are not “really working,” or asks to “nối ống backend” after UI scaffold.

## Pattern

1. Create a thin client helper for UI -> API calls:
   - default `content-type: application/json`
   - default tenant/company header like `x-company-id: demo-company`
   - parse `{ data, error, meta }`
   - throw user-visible error messages
   - generate idempotency keys for POST actions
2. Connect shared components first:
   - shared header actions
   - shared page template primary/secondary actions
   - common toast/loading states
3. Connect domain-critical pages next:
   - document check: scan, rerun, report, missing-doc letter
   - trade documents: generate doc pack/email/letter
   - buyer match: AI flow/action run
   - cost map: update map, create task, request quote
4. Ensure every clickable CTA does one of three things:
   - navigates to real route
   - opens modal/drawer with state
   - calls `/api/v1/...` and shows loading/success/error
5. Add minimal seed resources for newly-called endpoints so repository CRUD knows them.
6. Run a final clickable-control audit before saying “all wired”:
   - scan `src/**/*.tsx` for `<button>` without `onClick`, `type`, or `aria-label`
   - exclude unused template/auth/demo directories only after confirming they are not reachable in production nav
   - classify remaining buttons as route link, modal/drawer, filter/pagination/dropdown, backend action, or intentionally inert display control
   - wire shared components (`PageTemplate`, `FeatureWorkspace`, `SimpleScreen`, layout/header) first so many pages inherit behavior
7. Test both layers:
   - `curl -X POST /api/v1/...` for core backend actions
   - browser click for at least one visible CTA per critical page
   - `npm run lint && npm run build`

## ExportMate endpoint mapping used

```txt
POST /api/v1/document-checks
POST /api/v1/trade-documents/generate
POST /api/v1/ai-flows/run-document-check
POST /api/v1/ai-flows/run-buyer-match
POST /api/v1/reports
POST /api/v1/export-tasks
```

## Pitfalls

- Do not call “done” when UI only changes local state; user means backend pipe when they say app should work normally.
- Do not expose secrets to client. AI gateway keys stay server-side; UI calls app API only.
- If real AI env is absent, use structured fallback data but label implementation as fallback, not real provider success.
- Build catches type issues hidden by lint/browser, e.g. using a data object field that only exists on node wrapper.
- Avoid route-specific one-off handlers when a shared page template can route actions by context.
- User may say “nối hết chưa” after backend pipe claims. Treat as request for an exhaustive clickable-control audit, not reassurance. Report remaining UI-only controls explicitly.
- TailAdmin/ref assets can supply missing UI patterns (modal/table/drawer/filter), but keep product copy/domain IA ExportMate-specific and hide raw template pages from production nav.

## Verification snippet

```bash
curl -s -X POST http://127.0.0.1:3000/api/v1/document-checks \
  -H "content-type: application/json" \
  -H "x-company-id: demo-company" \
  -d '{"shipmentId":"shipment-af-2405-ham","market":"EU"}' | python -m json.tool
```
