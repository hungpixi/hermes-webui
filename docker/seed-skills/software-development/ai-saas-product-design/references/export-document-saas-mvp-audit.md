# Export document SaaS MVP audit notes

Use when auditing or finishing a domain SaaS dashboard built from TailAdmin or similar admin templates for an export/document/compliance product.

## Fast audit checklist

1. Read product/BA docs before judging UI scope. Common docs in this class: `phantich.md`, `ba.md`, pitch specs, sprint breakdowns.
2. Compare formal MVP modules against current navigation and route tree.
3. Separate three buckets:
   - Core MVP modules from BA.
   - Useful adjacent modules from pitch/prototype docs.
   - Template/demo leftovers that should not appear in production navigation.
4. Check auth routes separately from auth functionality: route presence is not login/session support.
5. Search UI text for forbidden/customer-facing terms from BA, not only route/path strings.
6. Verify with lint/build before saying UI is safe to ship.

## Common findings in TailAdmin-derived dashboards

Template leftovers often remain as routes/sidebar items:

```txt
/profile
/calendar
/blank
/basic-tables
/form-elements
/line-chart
/bar-chart
/alerts
/avatars
/badge
/buttons
/images
/modals
/videos
/error-404
```

Recommendation:
- Remove demo group from customer navigation.
- Keep files only if useful as hidden reference.
- Delete demo routes for production cleanliness when user says ship/MVP now.

## Export document SaaS core modules

Typical BA core list:

```txt
/dashboard                Tổng quan
/supplier-profiles        Hồ sơ nhà cung cấp
/product-profiles         Hồ sơ sản phẩm
/shipment-passports       Hộ chiếu lô hàng
/evidence-vault           Kho bằng chứng
/document-checks          Soi hồ sơ xuất khẩu
/market-requirements      Yêu cầu thị trường
/trade-documents          Bộ chứng từ
/buyer-leads              Tìm đối tác mua hàng
/outreach-campaigns       Chiến dịch chào hàng
/deal-opportunities       Cơ hội giao dịch
/service-partners         Đối tác dịch vụ
/reports                  Báo cáo
/ai-flows                 Luồng AI tự xử lý
/billing                  Thanh toán
```

Adjacent pages can be valid if pitch docs mention them:

```txt
/cost-map                 Bản đồ chi phí
/export-tasks             Việc cần làm
/product-specs            Spec sheet
/packaging-checks         Bao bì & nhãn
/traceability-lite        Truy xuất nguồn gốc bản nhẹ
/document-templates       Mẫu chứng từ
/quotations               Báo giá FOB/CIF
/landing-pages            Landing page sản phẩm
/chat-ai                  Chat AI
/admin-settings           Quản trị
```

## Auth audit and MVP fix

Say precisely:
- "Có route/login UI" if `/login`, `/signup`, `/forgot-password` render forms.
- "Chưa có auth thật" unless there is submit handler, validation, session/cookie/provider, backend integration, reset flow, protected routes.

Common quick fixes:
- Vietnamese copy with diacritics.
- Forgot password form should request only email.
- Add loading/empty/error states.
- For demo-only MVP, login/signup may set a local demo session and route to `/dashboard`, but mark this as demo behavior in UI copy.

## MVP-now implementation pattern

When user says they want an MVP immediately, prefer a demo-complete vertical slice over half-built production plumbing:

1. Hide/delete obvious template demo routes from navigation.
2. Keep all BA core routes present and buildable.
3. Make `/login`, `/signup`, `/forgot-password` usable as demo screens.
4. Seed two end-to-end demo cases:
   - `Cà phê Robusta - EU`: score `76/100`, missing residue test, farming log, growing-area coordinates, mismatch between Invoice and Packing List, tasks, request letter, buyer email/report output.
   - `Sầu riêng tươi - Trung Quốc`: score `68/100`, missing packing-house code, phytosanitary certificate, label/carton photos, tasks and risks.
5. Provide `/api/v1` mock-business endpoints with `x-company-id`, pagination, structured error shape, tenant filtering, and idempotent action outputs.
6. Stub paid/AI/document actions with structured saved records rather than loose chat text:
   - `POST /v1/document-checks`
   - `POST /v1/trade-documents/generate`
   - `POST /v1/reports`
   - `POST /v1/ai-flows/run-document-extraction`
   - `POST /v1/ai-flows/run-document-check`
   - `POST /v1/ai-flows/run-trade-document-pack`
   - `POST /v1/ai-flows/run-buyer-match`
   - `POST /v1/payments/sepay/create-qr`
   - `POST /v1/billing/use-credit`
7. Run `npm run lint` and `npm run build`; do not claim ready before verification completes.

## Export document product gaps to flag

If only UI/demo endpoints exist, call out missing production layers:
- Real auth/session/route protection.
- DB models/Prisma/PostgreSQL or equivalent persistence.
- Swagger/OpenAPI and DTO validation.
- AI document extraction: Docling/OCR, Flowise Prediction API, AI run logs, retry/failure states, structured saved outputs.
- Document generation: DOCX templates and PDF export or explicit placeholders.
- Payment: SePay QR/webhook, credit balance/ledger, pay-per-document-check/monthly plans.
- End-to-end production demo cases with real file processing, score, missing docs, mismatch fields, checklist, request letter, buyer email, PDF/DOCX.

## Reporting style for this user

Keep answer terse Vietnamese. Use buckets:

```txt
Có rồi
Dư
Thiếu
Nên làm tiếp
```

Avoid long narratives. Mention exact paths/routes when useful.
