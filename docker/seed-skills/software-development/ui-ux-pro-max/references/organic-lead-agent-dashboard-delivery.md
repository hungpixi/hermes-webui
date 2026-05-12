# Organic Lead Agent Dashboard Delivery Pattern

Session date: 2026-05-10

Use when building an Organic Lead Engine/TailAdmin SaaS dashboard slice for this user.

## User workflow/format preferences captured

- Keep approved/shared TailAdmin page as canonical data/function presentation. Do not heavily reformat; incrementally replace data, routes, and feature modules.
- Plans and implementation reports should be HTML files for readability, with short chat summary containing paths/URLs.
- Use `gh` for GitHub repo/remote ops; prefer `gh repo create/view/clone` + HTTPS remotes.

## End-to-end slice that worked

Dashboard repo:

```txt
D:/code/organic-lead-dashboard
https://github.com/hungpixi/organic-lead-dashboard
https://organic-lead-dashboard.phamphunguyenhung.com/
```

Cron repo:

```txt
D:/code/cronjob
https://github.com/hungpixi/cronjob
```

### 1. TailAdmin baseline

- Copy TailAdmin into new project, remove old `.git`, rename package.
- Keep TailAdmin sidebar/header/card/table grammar.
- Add `output: "standalone"` to `next.config.ts` for Docker/Dokploy.
- Add Dockerfile using Next standalone output.
- Avoid `npm audit fix --force` unless user explicitly approves; it can break Next/TailAdmin dependency tree.

### 2. 9Router logo generation

Use skill `ai-gateway-9router` + upstream `9router-image` docs.

Discovery:

```bash
curl "$NINEROUTER_URL/v1/models/image"
```

Working generation pattern:

```python
payload = {
  "model": "cx/gpt-5.4-image",
  "prompt": "Professional SaaS logo icon for Organic Lead Engine: blue gradient AI agent motif, abstract neural node orbiting a sprouting growth arrow and search lens, layered meaning: organic SEO growth, autonomous agent, data pipeline, trust. Minimal vector mark, clean geometry, no text, transparent background if possible, premium B2B SaaS, artistic but simple, works as app logo and favicon, azure/cobalt/electric blue palette.",
  "size": "1024x1024",
  "n": 1,
  "response_format": "b64_json"
}
POST $NINEROUTER_URL/v1/images/generations
```

Save as:

```txt
public/images/logo/organic-lead-agent-logo.png
```

Visual QA lesson: generated mark looked good but busy. Use as dashboard/sidebar mark; later create simplified favicon/mono variants if brand maturity matters.

### 3. Light brand polish without reformatting

Patch only:

- `src/layout/AppSidebar.tsx` — logo + wordmark.
- `src/components/header/UserDropdown.tsx` — agent avatar/name/email.
- Keep original TailAdmin topbar/sidebar structure.

### 4. Dashboard data/API layer

Add shared data module:

```txt
src/lib/organic-lead-data.ts
```

Expose:

```txt
src/app/api/dashboard/route.ts
src/app/api/audit/route.ts
```

Routes should verify publicly:

```txt
/api/dashboard -> JSON with metrics/prospects/audits/automations
/api/audit?url=<url> -> JSON score/status/keywords/fixes/openingLine
```

### 5. Audit CLI/API

Add CLI:

```txt
tools/organic-lead-audit.mjs
```

Package script:

```json
"audit:url": "node tools/organic-lead-audit.mjs"
```

Verify:

```bash
npm run audit:url https://doanhnghiep1nguoi.io.vn/seo-machine.html
```

### 6. Prospect enrich cronjob

In `hungpixi/cronjob`, add:

```txt
src/jobs/prospect-enrich-daily.mjs
```

Use 9Router `/v1/search` with provider `brave`, fallback to seed prospects if search/env unavailable. Write CSV + MD outputs to `REPORT_DIR || /tmp/cronjob-reports`.

Patch `src/index.mjs` to call it after resource miner when:

```env
PROSPECT_ENRICH_ENABLED=true
```

Dokploy env needed (do not print secret values):

```env
NINEROUTER_URL=...
NINEROUTER_KEY=...
SEARCH_PROVIDER=brave
PROSPECT_ENRICH_ENABLED=true
```

### 7. Verify/deploy gates

Dashboard local:

```bash
npm run build
npm run audit:url https://doanhnghiep1nguoi.io.vn/seo-machine.html
```

Dashboard public:

```txt
/ -> HTTP 200, contains Organic Lead / logo marker
/api/dashboard -> HTTP 200
/api/audit?... -> HTTP 200
```

Cron local:

```bash
npm run check
REPORT_DIR=./reports-out npm run run:prospect-enrich
```

Dokploy:

- Use `/api/application.update` with env, then `/api/application.deploy`.
- Poll `/api/deployment.all?applicationId=<id>` until `done`.
- Never dump Dokploy env because it can expose secrets.

## Session-proven commits

Dashboard:

```txt
8c0546f Add AI agent logo and organic lead data layer
40f91c7 Polish agent identity and implementation report
```

Cronjob:

```txt
0e3ce17 Add prospect enrich daily job
```

## Pitfalls

- Do not dump `/api/gitProvider.getAll` or app configs verbatim; they can include GitHub app private keys/client secrets. If needed, print only IDs/names.
- Do not expose internal 9Router/cx/ds2api names in customer-facing UI copy; OK in internal admin/report docs.
- DNS may have brief local resolver misses after Cloudflare create; retry public verify rather than treating as app failure.
