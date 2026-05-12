# Interactive SaaS Dashboard MVP Pattern

Use when turning a TailAdmin/Next.js SaaS dashboard from static demo screens into a credible clickable MVP.

## Workflow lessons

1. Do not claim "complete" after only routes/build pass. State exact level: UI/static, linked demo, API-backed mock, or real backend.
2. After wiring a page, run browser QA on the live dev server: navigate, screenshot, click visible CTAs, tabs, nodes, modals, and header actions.
3. If a visible button has no real backend yet, still add deterministic MVP feedback: toast/status panel, optimistic local state, route navigation, or modal result. Dead buttons reduce trust.
4. For TailAdmin adaptations, remove hidden template/demo routes from both sidebar and filesystem before final MVP build if user asks whether anything is dư.
5. Keep auth distinction explicit: auth screens are not real auth until submit/session/route protection/backend exist. MVP demo can use localStorage but must be described as demo.

## Header and dashboard CTA wiring

Minimum acceptable MVP interactions:

- Header search opens quick-search modal with relevant result links.
- Create/import buttons open modals; import should at least accept/display chosen file name if implemented.
- Dashboard quick actions use real routes or API calls; do not leave `button` tags inert.
- Feature-card CTAs navigate to detail/workflow pages or show a generated result panel.
- AI buttons should show running/done/error state even if server action falls back to demo data.

## React Flow cost-map pattern

For a "map" or node-based compliance/cost/operations screen, prefer `@xyflow/react` over static cards/checklists.

Good MVP shape:

- Central React Flow canvas with custom TailAdmin-style nodes.
- Node categories: product, target market, mandatory cost item, optional cost item, risk item, service partner.
- Node fields: title, category badge, status badge, estimated cost range, confidence, priority.
- Color system: green completed, blue info, yellow needs input, red risk, gray optional.
- Click node updates inspector panel.
- Inspector shows why it matters, estimated cost/time/confidence, required docs, next actions, suggested partners.
- Tabs around the map: Map, Cost Table, 30-day Roadmap, Service Partners.
- On mobile/narrow layouts, inspector can stack below map; on desktop prefer right-side inspector.

## Verification checklist

Run:

```bash
npm run lint && npm run build
npm run dev
```

Browser QA:

- `/login` submit path works for demo auth.
- `/dashboard` has no old product/demo leftovers.
- Core route CTAs navigate somewhere useful.
- `/cost-map` renders node canvas, inspector, tabs, and clickable actions.
- Screenshot with browser vision; if it reports dead layout or hidden inspector, adjust breakpoint/layout.

## Pitfalls

- Static UI with many good-looking buttons is not enough; users will click and call out dead controls.
- Do not leave obvious old demo entities (e.g. unrelated fruit/product cases) on dashboard if BA requires specific demo cases.
- Build success is necessary but not sufficient; do web-app click-through QA before reporting MVP status.
- Avoid displaying raw labels like "Mock data" prominently on product screens; use smaller source/confidence wording.
