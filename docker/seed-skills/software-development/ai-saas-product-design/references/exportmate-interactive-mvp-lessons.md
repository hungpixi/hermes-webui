# ExportMate interactive MVP lessons

Session lesson from building an export/document SaaS MVP on TailAdmin/Next.js.

## Ship-readiness checks user cared about

- Do not call an MVP “complete” if major buttons are decorative. User expects visible CTAs to do something: navigate, open a modal, update state, call mock API, or show toast.
- Shared header actions must be wired because they appear on every dashboard page. Check search, create, import/upload, AI action, notifications, help, settings, and account menu.
- If a button cannot perform real backend work yet, wire an honest demo behavior: local state, toast, modal, link, mock API call, or “queued/chờ rà soát” state.
- After wiring, verify by browser clicking and screenshot/vision, not only by lint/build.

## Cost Map screen pattern

For “bản đồ chi phí tuân thủ”, a checklist/card dashboard is not enough. Build a real map:

- Use `@xyflow/react` / React Flow for node-based UI.
- Central canvas: Product -> Market -> Required cost/risk/service nodes.
- Custom nodes show title, category, status badge, cost range, confidence, priority.
- Color semantics: green completed, blue info, yellow needs input, red risk, gray optional.
- Node click opens a Cost Inspector panel.
- Inspector shows why it matters, estimated cost, documents, next actions, service partners.
- Top summary cards: total estimated range, mandatory count, risk count, readiness score.
- Tabs: Map, Cost Table, 30-day Roadmap, Service Partners.
- Action buttons must respond: AI update map toast, create task from node, request partner quote, reflect created/requested state.

## Header shared shell pattern

For TailAdmin-style SaaS dashboard shell:

- Search box should be a button/input that opens quick search modal with route results.
- Create button opens form modal and save draft produces feedback.
- Import button supports file selection and displays selected filename before import.
- AI action can call mock/API endpoint and then show toast.
- Bell opens notification dropdown with actionable links.
- Help/settings are links to `/support` and `/settings`.
- Account/avatar opens menu with users, billing, and demo logout.
- Ensure actions are visible at common laptop widths; avoid hiding all header actions behind too-large breakpoints (`xl`) without a mobile alternative.

## Persona/copy audit for domain SaaS dashboards

When user asks to “read toàn bộ UI” or says the UI still feels hardcoded/template-like:

1. Re-read product/pitch/BA docs and identify primary users before changing components.
   - Export/document SaaS personas were: small business/HTX owner, export document staff, export sales—not dev/admin users.
2. Rewrite first viewport around the user’s job-to-be-done: “what do I need to do today?” not “dashboard stats”.
3. Remove user-visible implementation language:
   - `Mock data`, `TailAdmin`, `demo`, `theo BA`, `AI estimate`, internal route names, and generic admin/template language.
4. Replace English jargon unless it is customer-facing in this market:
   - `buyer` -> `nhà nhập khẩu` / `đối tác mua hàng`
   - `Spec sheet` -> `bảng thông số sản phẩm` / `hồ sơ gửi nhà nhập khẩu`
   - `lab` -> `phòng kiểm nghiệm`
   - `Cost Inspector` -> `Chi tiết chi phí`
5. Navigation labels should name user outcomes and artifacts: “Soi hồ sơ xuất khẩu”, “Bản đồ chi phí”, “Việc cần làm”, “Hồ sơ gửi nhà nhập khẩu”, “Trang chào hàng”.
6. In screenshots, inspect not only layout but trust signals: placeholder images, tiny prototype hints, and mixed jargon can make real product data feel fake.
7. Be careful with global text replacement; do not replace substrings inside identifiers, paths, or type names (`BuyerLeadsPage`, `label`, `/buyer-leads`). Prefer targeted literals or run lint immediately after broad copy edits.

## Verification recipe

1. Run `npm run lint && npm run build`.
2. Start dev server.
3. Browser-test at least:
   - `/dashboard` header/search/buttons and first-viewport persona fit.
   - `/document-checks` core case.
   - `/cost-map` map nodes, tabs, inspector actions, and Việt hóa of business copy.
4. Use browser screenshot/vision to catch layout/trust mistakes such as hidden header actions, placeholder imagery, dev/mock jargon, or inspector stacking when right-side panel was required.
5. Commit/amend only after build and click/screenshot checks pass.
