# ExportMate AI-agent dashboard pattern

Session-proven for ExportMate-style MVP dashboards when user wants "AI agent vibe" and feature-first implementation over pixel-perfect reference matching.

## Trigger

Use when building/refining a SaaS dashboard where the user wants:

- realistic feature behavior, not exact screenshot cloning;
- click feedback for all visible actions;
- batch-by-batch page/function completion;
- AI agent command/control feel on top of a TailAdmin-like app shell;
- 15.6-inch laptop usability.

## Pattern

1. Treat reference screenshots as feature inventory, not visual law.
2. Add an **AI Agent control** strip near the top of the dashboard:
   - mode selector (e.g. review docs, find buyers, generate docs, prioritize urgent work);
   - domain filters (market/status/priority);
   - dynamic counts (risks/tasks/leads);
   - primary `Run agent` button;
   - reset button.
3. Derive filtered dashboard sections with `useMemo`:
   - risk/compliance table filtered by market + risk status;
   - task list filtered by priority;
   - buyer/opportunity list filtered by market.
4. Wire all row actions to backend-like idempotent endpoints, even if mock:
   - document analysis;
   - task/checklist creation;
   - document generation;
   - evidence/request workflow;
   - buyer match/outreach.
5. Add toast feedback for every button/action. User explicitly wants "click cái gì cũng phản hồi".
6. Keep mock API endpoints idempotent when actions can be re-clicked during demo.
7. For 15.6-inch fit, reduce vertical cost before removing content:
   - compact hero copy;
   - shorter KPI cards;
   - tighter gaps;
   - internal scroll inside dense cards;
   - keep first viewport showing command bar + KPIs + primary work panels.

## Verification checklist

Run:

```bash
npm run lint && npm run build
```

Smoke:

- `/dashboard` renders at ~1262x624 or 1366x768.
- No horizontal overflow.
- Agent mode/filter controls change visible counts/rows.
- `Run agent` and row actions call `/api/v1/...` endpoints and show toast.
- Backend smoke returns 2xx for action endpoints.
- Git status preserves reference assets/untracked files if user said not to add them.

## Pitfalls

- Do not report "pixel-perfect" if only feature parity was implemented.
- Do not over-compact until text becomes unreadable; laptop-fit means usable, not zero scroll.
- Browser automation may miss transient toasts; verify endpoints directly with curl if needed.
- Avoid session-specific plan files being staged unless user asks to commit `.hermes/plans`.

## Example endpoint set

```txt
/api/v1/ai-flows/run-dashboard-agent
/api/v1/document-checks/analyze
/api/v1/export-tasks/create
/api/v1/trade-documents/generate
/api/v1/evidence-vault/request
/api/v1/ai-flows/run-buyer-match
```
