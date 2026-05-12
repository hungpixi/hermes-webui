# SEO Skill Sync Agent Dashboard Pattern

Session source: Organic Lead Engine dashboard, 2026-05.

Use when a SaaS/AI-agent dashboard must expose local Hermes skills as product capabilities and keep them current over time.

## Goal

Make skills a first-class capability layer in dashboard UI:

- `/plan` for planning workflows.
- `/research` for research workflows.
- `/seo-skills` for capability library.
- `/api/dashboard` for machine-readable skill metadata.
- Cron sync so newly edited/created skills update dashboard without manual copy/paste.

## Implementation pattern

1. Keep approved TailAdmin/shared dashboard layout intact.
2. Add a typed capability model, e.g. `src/lib/seo-skills.ts`:
   - `id`
   - `name`
   - `stage`
   - `description`
   - `inputs`
   - `outputs`
   - `checks`
3. Add skill pages:
   - `src/app/(admin)/plan/page.tsx`
   - `src/app/(admin)/research/page.tsx`
   - `src/app/(admin)/seo-skills/page.tsx`
4. Add reusable interactive runner component:
   - `src/components/seo/SeoAgentRunner.tsx`
   - Inputs: topic/seed query, URL/project, selectable skills.
   - Output: deterministic plan/research brief first; LLM endpoint later.
5. Extend `src/app/api/dashboard/route.ts` with:
   - `seoSkills`
   - `planWorkflow`
   - `researchWorkflow`

## Sync script pattern

Create `tools/sync-seo-skills.mjs` that:

1. Reads `HERMES_SKILLS_DIR` or defaults to `%LOCALAPPDATA%/hermes/skills`.
2. Scans `skills/seo/*/SKILL.md`.
3. Extracts title, description, workflow/check bullets.
4. Infers stage (`plan`, `research`, `publish`, `audit`) from name/content.
5. Regenerates `src/lib/seo-skills.ts` with an `AUTO-GENERATED` header.
6. Run `npm run build` after sync.

NPM script:

```json
"sync:seo-skills": "node tools/sync-seo-skills.mjs"
```

## Cron pattern

Create a Hermes cron job (local or deployment-control layer) with flow:

1. `npm run sync:seo-skills`
2. `npm run build`
3. If no git changes: report `SEO skills unchanged` and stop.
4. If changed and build passes:
   - commit `Sync SEO skills into dashboard`
   - push using `gh`/HTTPS remote conventions
   - trigger Dokploy deploy
   - verify `/plan`, `/research`, `/seo-skills`, `/api/dashboard`
5. Never print secrets; redact as `[REDACTED]`.

Example job name:

```txt
sync-seo-skills-into-organic-lead-dashboard
```

## Pitfalls

- Do not manually paste skill content into multiple pages; regenerate one model file.
- Do not let sync failure deploy stale/broken app; build must pass before push/deploy.
- Do not expose internal secrets/API keys in dashboard skill metadata.
- Do not over-customize TailAdmin layout while adding skill pages; use existing cards/forms/tables.
- Do not treat generated skill metadata as source of truth; Hermes skill library remains source of truth.

## Upgrade path

After deterministic runner works:

1. Add `/api/agent/run` server endpoint.
2. Call 9Router or chosen provider server-side.
3. Save generated JSON/HTML reports.
4. Show saved reports in `/reports`.
5. Add auth/session guard before private/paid usage.
