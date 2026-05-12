# SEO Agent Capability Dashboard Pattern

Session source: Organic Lead Engine dashboard build, May 2026.

## When to use

Use when turning a TailAdmin-style SaaS dashboard into an AI-agent product that can plan/research/audit SEO work through reusable skills.

## User-specific UI rule

For this user, keep the approved TailAdmin/shared page structure as the canonical scientific data/function presentation. Do not heavily reformat once accepted. Add capabilities as pages/data/API routes inside the existing IA.

## Loaded SEO capability model

Represent SEO skills as an agent capability layer, not loose docs:

```ts
export const seoSkills = [
  {
    id: "seo-blueprint-vi",
    name: "SEO Blueprint",
    stage: "plan",
    description: "End-to-end SEO operating system",
    inputs: ["business goal", "seed topics", "site goal"],
    outputs: ["SEO roadmap", "content brief", "refresh queue"],
    checks: ["one intent per URL", "title/meta unique", "schema valid"],
  },
]
```

Good stages:

- `plan` — roadmap, clusters, briefs, schema/internal-link plan.
- `research` — SERP/PAA/AI Overview, entities, proof gaps.
- `audit` — on-page/readability/schema/helpful content checks.
- `publish` — answer blocks, JSON-LD, llms.txt, validation.

## Pages to add

- `/plan` — SEO Planning Agent. Show workflow + plan-stage skills.
- `/research` — SEO Research Agent. Show research/audit skills and checks.
- `/seo-skills` — skill library. Show each skill’s inputs, outputs, checks.
- Optional `/api/dashboard` expansion: include `seoSkills`, `planWorkflow`, `researchWorkflow`.

## SEO skills proven useful

- `seo-blueprint-vi`
- `seo-fundamental-roadmap-vi`
- `entity-topical-seo-vi`
- `ai-schema-geo-seo-vi`
- `yoast-style-content-audit-vi`
- `google-seo-starter-guide-vi`
- `ecommerce-seo-vi`

## Implementation sequence

1. Read/inspect SEO skills with `skills_list(category="seo")` and `skill_view`.
2. Condense them into a product data model (`src/lib/seo-skills.ts`).
3. Add `/plan`, `/research`, `/seo-skills` pages using TailAdmin cards/tables/lists.
4. Add nav links without moving existing accepted pages.
5. Extend `/api/dashboard` so the agent product can consume capabilities.
6. Build, deploy, verify routes.

## Verification

```bash
npm run build
```

Check routes:

```txt
/plan
/research
/seo-skills
/api/dashboard
```

Quality bar:

- Build passes.
- Routes return 200 after deploy.
- `/api/dashboard` contains `seoSkills` plus workflows.
- UI stays consistent with TailAdmin spacing/cards/table grammar.
- No secrets or internal provider keys shown in public UI.
