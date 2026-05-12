# Agent Run Reports + Sellability Loop Pattern

Session pattern from Organic Lead Engine dashboard productionization.

## When to use

Use for AI-agent SaaS dashboards where users need to run an agent, get useful artifacts, export them, and iterate toward a sellable product.

## Server-side agent runner

Add an API route like:

- `POST /api/agent/run` — accepts `mode`, `topic`, `url`, `skills`.
- Calls provider server-side only; never expose keys to client.
- Uses selected skill metadata to construct the prompt.
- Returns `{ ok, reportId, model, markdown, jsonUrl, htmlUrl }`.
- Include a deterministic local fallback report so the UI and workflow do not break when provider/network is unavailable.

For 9Router/OpenAI-compatible calls:

- Keep `NINEROUTER_URL`, `NINEROUTER_KEY`, and model env server-side.
- Discover available chat models before choosing defaults; avoid stale assumptions like route names from another environment.
- If deployed server cannot reach local 9Router, preserve fallback and report the network/config gap rather than blocking the product flow.
- Customer-facing UI should say generic phrases like `AI provider`, `model route`, `report engine`; do not expose internal provider names or keys.

## Report storage/export

Runtime report APIs:

- `GET /api/reports` — list report metadata.
- `GET /api/reports/:id` — JSON export.
- `GET /api/reports/:id?format=html` — HTML export.

Implementation notes:

- Store runtime reports under `AGENT_REPORT_DIR` or `/tmp/...` for container-safe ephemeral storage.
- Save both `{id}.json` and `{id}.html`.
- Use simple Markdown-to-HTML conversion for first version; avoid adding large dependencies unless product needs rich rendering.
- Link JSON/HTML exports directly from the runner UI.

## Interactive runner UX

On `/plan` and `/research` pages:

- Fields: topic/seed query, URL/project, skill checkboxes.
- Button: `Run agent` with loading state.
- Output: model badge, JSON export link, HTML export link, markdown preview.
- Failure state: readable error in preview area, not a crashed page.

## Auth landing polish for sellable SaaS

For login/register/forgot pages, use the auth layout as a product landing surface:

- Split layout: focused form on one side, value proof panel on the other.
- Brand block, strong outcome headline, concise Vietnamese-first explanation.
- Show real product capabilities (e.g. skill orchestration, agent reports, JSON/HTML exports), not generic SaaS filler.
- Keep metrics clearly tied to real system facts or label as demo; avoid fake revenue/users/testimonials.
- Verify at common laptop viewport; make sure form width does not inherit old template `lg:w-1/2` assumptions when the parent layout changes.

## Autonomous sellability loop

For productionizing a SaaS dashboard with minimal back-and-forth, create a bounded cron job:

- Repeat ~20 runs, every few hours.
- Each run: inspect repo, sync skills, build, score, choose 1–3 high-leverage fixes, implement, build, commit/push, deploy, verify.
- Stop/no-op when strict score reaches 95/100.
- Use a harsh rubric covering: Vietnamese market fit, onboarding/auth, agent capability, report usefulness, dashboard actionability, UI polish/accessibility, trust/privacy, runtime reliability, sellability/pricing/value proof.
- Keep changes incremental and production-safe; do not deploy if build fails.
- Do not recursively create more cron jobs.

## Verification

Minimum before reporting done:

- `npm run build`
- Route verification for changed pages/API.
- If deployed, public HTTP checks for auth pages, core agent pages, and API exports.
- Browser visual check for high-impact auth/landing changes when available.

## Pitfalls

- Do not let provider failure block export/report UX; use fallback and surface config gap.
- Do not print env values or tokens while setting provider/Dokploy env.
- Do not over-rewrite TailAdmin-approved dashboard structure; add capability inside it.
- Do not use generic English SaaS copy for Vietnamese sellability; Vietnamese-first copy and local business workflow matter.
- Do not confuse ephemeral `/tmp` report storage with durable product storage; flag database/object storage as next hardening step.
