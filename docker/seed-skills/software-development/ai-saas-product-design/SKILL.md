---
name: ai-saas-product-design
description: Plan and design AI-first SaaS products, sales landing pages, dashboards, and plugin-based workflows. Use for BA specs, UX IA, landing-to-dashboard separation, AI provider abstraction, and scalable content/automation products.
---

# AI SaaS Product Design

Use when user asks to design/rebuild a SaaS landing page, AI-first dashboard, plugin workflow, product BA document, or service-to-SaaS roadmap.

## Core principle

Separate **sales landing** from **product dashboard**.

- Landing sells outcome, trust, pricing, and next step.
- Dashboard handles dense operations, pipelines, tables, settings, and detailed AI output.
- Do not dump full dashboard output into hero or first viewport.
- Hero must fit common viewport sizes like `1366x768` without cropping right/bottom content.

## BA-first workflow

When user says current UI feels wrong, unclear, or wants product to match foreign SaaS quality:

1. Pause coding if product direction is unclear.
2. Write/update BA spec before rebuilding UI.
3. Define personas, pains, outcomes, IA, user flows, entities, MVP scope, and success metrics.
4. Only then rebuild UI against the spec.

Recommended BA sections:

- Goal and business model
- Personas and pains
- Public product positioning
- Landing page structure
- Dashboard information architecture
- Core workflows
- Data/schema notes
- AI/provider abstraction
- UX/UI standards
- MVP scope and roadmap
- Success metrics
- Open questions

## Sales landing page rules

A SaaS landing page should be progressive and conversion-focused:

1. Hero: clear outcome + target user + primary CTA.
2. Problem: show current pain.
3. Solution workflow: 3–5 clear steps.
4. Product preview: dashboard mockup/screenshot, not full app.
5. Use cases/personas.
6. Pricing/service packages.
7. FAQ handling objections.
8. Final CTA / lead form.

Rules:

- One primary CTA per section.
- Keep language consistent (do not mix English/Vietnamese unless intentional).
- Use customer outcome language, not internal tool names.
- Minimize repeated copy buttons/forms on landing.
- Use demo/dashboard as next step, not entire landing body.
- Avoid saying “AI slop” in Vietnamese-facing polished marketing unless user wants edgy copy.

## Dashboard UX rules

Dashboard can be dense; landing cannot.

For AI-first dashboards:

- Use a task-oriented home: “what needs action this week?”
- Put AI outputs behind pipeline/detail views.
- Every AI output needs actions: regenerate, edit, approve, reject.
- Use status badges and review queues.
- Keep tables for batch operations.
- Use side panels for AI suggestions and explanations.
- Add human review gate for high-volume content/automation.

When adapting an admin template such as TailAdmin for a domain SaaS:

- First inventory **all** existing product routes and **all** template reference routes, not only the visible sidebar.
- Read BA/product docs in the repo before deciding IA; derive missing pages from both formal scope and pitch/prototype docs.
- Use shared page/screen components to scaffold missing routes quickly when user prioritizes complete scope over bespoke UI.
- Keep template reference/demo pages available under a separate group when user asks to “copy shared pages” or preserve TailAdmin examples; remove/hide them from production navigation when auditing for ship-readiness.
- Treat auth routes separately from auth capability: `/login` and `/signup` pages are only UI until session, validation, submit handlers, provider/backend integration, and route protection exist.
- Wire navigation and route files immediately after scaffolding pages; then run lint/build to catch route/import drift.
- Produce an HTML page-plan/report artifact in the repo when user asks for “báo cáo bằng html,” with page list, route, status, source document, and next-step priorities.
- For export/document SaaS MVP audits, use `references/export-document-saas-mvp-audit.md` for route buckets, TailAdmin leftovers, auth distinction, and production gaps.
- For interactive MVP hardening lessons from ExportMate (shared header wiring, visible CTA behavior, React Flow cost-map pattern, and screenshot click checks), use `references/exportmate-interactive-mvp-lessons.md`.
- For backend-pipe MVP hardening (UI CTA -> `/api/v1` -> handler/action/repository/AI fallback -> toast/result), use `references/exportmate-backend-pipe-lessons.md`. Include a final button audit pass before claiming “all wired.”
- For DDD demo hardening (architecture page, shared contracts/server domain/API adapter separation, demo API smoke tests, export/share CSV), use `references/exportmate-ddd-demo-hardening.md`.
- For screenshot-driven ExportMate/dashboard implementation from reference images, use `references/exportmate-screenshot-driven-dashboard-build.md`: extract widgets/interactions per screenshot, implement data parity + shared interactions first, then bespoke pixel-perfect pages in small batches.
- For quick aptitude/IQ solver apps using 9Router + Dokploy, use `references/aptitude-solver-9router-dokploy.md`; if tests are image-based, render question/solution PDF pages and show them in the study UI instead of relying on extracted text alone. Include Quizlet-like flashcard learning when user wants to study examples: front = question image, back = solution image + per-question explanation/rationale.

Common routes:

```txt
/                  Sales landing
/app               Dashboard home
/app/projects      Project/workspace list
/app/keywords      Keyword/input pipeline
/app/briefs        Brief queue
/app/articles      Drafts/articles
/app/review        Human review queue
/app/publish       Publish queue
/app/analytics     Performance loop
/app/settings      API/provider/brand/integration settings
```

## AI provider abstraction in SaaS

Do not expose internal gateway/provider implementation in public SaaS copy unless it is part of the product brand.

Prefer customer-facing terms:

- OpenAI-compatible API
- Custom API Base URL
- Bring your own AI key
- Multi-provider support
- Provider fallback
- Cost controls
- Model routing by task type

Internal/private terms can remain in implementation notes, but should not appear in sales copy:

- private gateway names
- internal model route prefixes
- free auth routes
- secret provider mechanics

Task-role settings are clearer than raw model talk:

```txt
Fast model: classification/metadata
Quality model: brief/draft
Review model: QA/risk checks
Embedding model: internal linking/search
Fallback provider: optional
Monthly budget cap: optional
```

## High-volume AI content guardrails

For products that generate dozens/hundreds of content pieces per week:

- Batch generation must include clustering and deduplication.
- Do not auto-publish everything by default.
- Use review scores and risk flags.
- Detect cannibalization/thin content/duplicate intent.
- Schedule publishing rather than dumping hundreds of posts at once.
- Close loop with analytics: GSC/GA4, index status, clicks, impressions, conversions.

Workflow:

```txt
import keywords/URLs/sitemap/GSC
-> cluster and prioritize
-> approve batch
-> generate briefs
-> generate drafts
-> review risk and brand voice
-> push to CMS as drafts
-> schedule publish
-> monitor and refresh
```

## WordPress plugin-as-bridge pattern

For AI content SaaS + WordPress:

Plugin should be a bridge, not the whole app:

- Connect site to SaaS/dashboard.
- Sync categories/tags/posts.
- Pull approved content packages.
- Push drafts/scheduled posts.
- Set SEO plugin metadata (Yoast/RankMath/AIOSEO later).
- Log publish errors.
- Keep settings simple: API URL, license key, default author/category/status, SEO plugin, approval mode.

Content package schema should include:

```json
{
  "title": "...",
  "slug": "...",
  "excerpt": "...",
  "content_html": "...",
  "meta_title": "...",
  "meta_description": "...",
  "focus_keyword": "...",
  "secondary_keywords": [],
  "categories": [],
  "tags": [],
  "internal_links": [{"anchor":"...","url":"..."}],
  "faq": [{"question":"...","answer":"..."}],
  "schema": {},
  "image_prompt": "...",
  "cta": "...",
  "review_score": 0,
  "review_notes": []
}
```

## Visual/UI standards

Use common SaaS UI/UX patterns:

- 8px spacing system.
- Max width 1180–1240px.
- Clear grid, usually 55/45 hero split.
- Hero preview max height around 420–480px.
- AA contrast for muted text.
- 16–24px card radius.
- Sticky/simple nav.
- Responsive: single column on mobile.
- Avoid horizontal overflow and cropped mockups.
- Landing first viewport should show hero, CTA, and preview cleanly.

## Research-backed solopreneur idea reports

When the task is ideation for AI/SaaS solopreneur opportunities, especially China-tech-to-Vietnam arbitrage, avoid generic idea lists. Use the detailed workflow in `references/china-tech-solopreneur-research.md`.

Key rules:

- Do not rely only on GitHub Trending; search China-specific sources and models already showing demand.
- Avoid broad search queries like `China AI 2026`; use source-specific queries and Chinese terms.
- If user wants speed, avoid slow subagent delegation and run direct/manual research first.
- Save durable reports as HTML in the codebase, typically `D:\code\startup-idea\reports\YYYY-MM-DD-china-tech-vn-solopreneur.html`.
- Include source URLs, VN gap, buyer, pain near money, MVP, Lovable/Cursor prompt, first sales channel, pricing, metric, moat, risk, and score.
- Prefer ideas that can be sold before they are built.

## Quick AI utility web apps

When user asks for a lightweight AI solver/tool app, especially using their private 9Router endpoint and Dokploy, keep scope small: FastAPI backend, static bright/readable HTML, OpenAI-compatible `/v1/chat/completions`, env-driven credentials, Dockerfile/Compose or Nixpacks/Procfile if Docker is unavailable, `/health`, and concise Vietnamese answer formatting. If source PDFs/docs exist, add a `/knowledge` study page backed by normalized knowledge JSON or a markdown second-brain vault. For an aptitude/case/logic solver reference, use `references/aptitude-solver-9router-dokploy.md`.

## User-specific preference signals

If this user says “100 tiếng Việt,” make the landing/sales copy fully Vietnamese first. Product terms like SEO, WordPress, API are fine. Avoid mixed English CTAs unless explicitly targeting international users.

If user says “viết tài liệu BA trước,” produce a markdown BA spec in the repo before implementing UI.

If this user asks for startup/solopreneur research, bias toward terse Vietnamese, concrete business opportunities, and archived HTML reports in the repo rather than long chat-only essays.
