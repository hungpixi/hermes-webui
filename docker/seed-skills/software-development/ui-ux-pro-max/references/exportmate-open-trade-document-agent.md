# ExportMate open trade-document agent pattern

Session-proven for turning `Bộ chứng từ` from static table into an AI-agent document generator using open schema references from a BA/spec document.

## Trigger

Use when user asks ExportMate/AI SaaS to generate export documents from templates, especially after pointing to BA docs with GitHub/open-source references.

## Source discovery pattern

1. Read local BA/spec markdown first, not web search first.
2. Extract GitHub URLs with a simple regex and normalize away tracking params.
3. Clone only reference repos needed for the immediate feature into `ref/open-source/`; do not stage these clones unless user explicitly wants vendored refs.
4. For trade-document MVP, useful references were:
   - `FIATA/eFBL` for party/consignment/package references.
   - `uncefact/spec-JSONschema` for `CrossIndustryInvoice`, `CrossIndustryExportPackingList`, quotation/order structures, certificates.
   - `hmrc/customs-declarations` for supporting-document workflow/audit-trail ideas.

## Product/UI pattern

Build `/trade-documents` as an agent cockpit, not just a generic table:

- left template library;
- center selected template, shipment data to be filled, draft preview;
- right guardrails and existing tracked documents;
- `AI điền mẫu` primary action;
- `Share link` and `Export CSV` actions;
- market/status filters;
- toasts for all clicks.

Starter templates that are broad enough for MVP:

- Commercial Invoice — reference `UN/CEFACT CrossIndustryInvoice` + FIATA consignment refs.
- Packing List — reference `UN/CEFACT CrossIndustryExportPackingList` + FIATA package data.
- Proforma Invoice — reference UN/CEFACT quotation/invoice structures.
- Sales Contract Draft — reference order/contract data concepts and audit trail; mark as draft, not legal advice.
- Missing Documents Request Letter — reference supporting-docs workflow and document-check output.

## Backend pattern

Create a thin server domain service such as:

```txt
src/server/domain/exportmate/document-template-service.ts
```

Expose:

```txt
GET  /api/v1/trade-documents/templates
POST /api/v1/trade-documents/generate-from-template
```

Generation output should include:

```ts
{
  documentType,
  title,
  language,
  status: "cho_duyet",
  approvalState: "can_nguoi_duyet",
  content,
  template,
  fields,
  missingFields,
  sourceRefs,
  outputFormats: ["DOCX", "PDF", "HTML"]
}
```

Keep API idempotent for generation actions; include `idempotencyKey` in UI calls.

## Guardrails to show in UI

- Do not copy copyrighted templates; use open schema/field structure and write ExportMate-owned templates.
- Always show source refs for audit.
- Generated docs are drafts requiring human approval before buyer/forwarder use.
- Warn about missing required fields and inconsistencies.
- Legal/official certificates are request/draft aids, not production legal documents.

## Verification

Run:

```bash
npm run lint && npm run build
curl -s -o /dev/null -w '%{http_code}' http://localhost:3000/api/v1/trade-documents/templates
curl -s -o /dev/null -w '%{http_code}' -X POST http://localhost:3000/api/v1/trade-documents/generate-from-template -H 'content-type: application/json' -d '{"idempotencyKey":"smoke-template","templateId":"commercial_invoice"}'
```

Browser smoke:

- `/trade-documents` renders.
- Clicking `AI điền mẫu` creates preview containing the selected template title and human-approval note.
- Share/export/filter/template buttons show visible feedback.

## Pitfalls

- `exportmateApi` may be a callable helper, not an object with `.post`; inspect before using.
- Do not commit cloned upstream repos under `ref/open-source/` by accident.
- Keep wording honest: "tham chiếu mở/schema" not "mẫu chính thức" unless source truly provides a production template.
