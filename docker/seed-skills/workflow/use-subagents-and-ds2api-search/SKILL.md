---
name: use-subagents-and-ds2api-search
description: Use subagents and 9Router/ds2api for slow/free web-search-backed research workflows. Trigger when user asks to research/prospect/compare many items, says use subagent, or work can run in parallel.
---

# Use Subagents + ds2api Search

Use when work benefits from parallel research or slow/free web-search-backed models.

## User Preference

- Try/use many independent Hermes `delegate_task` subagents for suitable work, but do not make core progress depend on one slow child.
- Do not depend on `ds2api` for normal reasoning/code work.
- `ds2api` behind 9Router likely has web search because it wraps chat from web into an API, not a direct model API.
- `ds2api`/DeepSeek V4 is free / OK to use heavily, slow, and strongest for web search.
- Let slow `ds2api` search run while doing other deterministic work with normal tools/subagents.

## When To Use Subagents

Use `delegate_task` when task has independent chunks:

- prospect research by segment
- competitor/market research
- SERP/topic discovery
- code review plus implementation split
- deploy troubleshooting plus content work in parallel

Good pattern:

1. Split into up to 3 independent tasks.
2. Pass full context: repo path, target output format, language/tone, constraints.
3. Ask each subagent for verifiable handles: URLs, file paths, exact commands, HTTP statuses.
4. Verify any external side effects yourself before reporting success.

Do not delegate:

- single tool calls
- tasks needing user clarification
- operations where child claim cannot be verified

## 9Router / ds2api Use

Load env from Hermes secret file only:

```bash
set -a
source "$HOME/AppData/Local/hermes/secrets/user.env"
set +a
```

Discover models/capabilities:

```bash
curl "$NINEROUTER_URL/v1/models" -H "Authorization: Bearer $NINEROUTER_KEY"
curl "$NINEROUTER_URL/v1/models/web" -H "Authorization: Bearer $NINEROUTER_KEY"
```

If `ds2api` model appears, prefer it for research where web search matters and latency is acceptable.

Generic OpenAI-compatible call shape:

```bash
curl "$NINEROUTER_URL/v1/chat/completions" \
  -H "Authorization: Bearer $NINEROUTER_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model":"MODEL_FROM_DISCOVERY",
    "messages":[{"role":"user","content":"Research X. Include sources/URLs."}],
    "temperature":0.2
  }'
```

## Practical Workflow For Prospect Research

1. Use subagents by segment, e.g. `SaaS`, `professional services`, `local high-ticket`.
2. Each subagent returns CSV rows with:
   - company/domain
   - segment
   - evidence URL
   - pain angle
   - suggested opening line
   - CTA
3. Parent dedupes, validates URLs if possible, writes one CSV.
4. Do not invent emails. If no public email found, use contact page URL or LinkedIn/company URL.

## Dokploy Cronjob Project

Reserve hungpixi `cronjob` project/repo/Dokploy app for uploading/managing Hermes/workflow cronjob services only. Do not mix random app cron jobs into it unless user explicitly asks.

Use hybrid migration pattern:

1. Keep Hermes cronjobs for local chat-native delivery only when needed.
2. Convert durable jobs into a Dokploy cron service (`hungpixi/cronjob`) using normal scripts and scheduler loop.
3. Put secrets in Dokploy env, not repo.
4. Write reports to `/tmp/cronjob-reports` or send via webhook (`REPORT_WEBHOOK_URL`).
5. Keep slow `ds2api` search as background research; never block core deterministic work on it.

Session detail: see `references/hungpixi-cronjob-dokploy-2026-05-10.md`.

## Cron Reliability Pattern

When creating Hermes cron loops that user expects to run unattended:

1. After `cronjob(action="create"|"update"|"run")`, immediately check `cronjob(action="list")` for `last_run_at`, `last_status`, `next_run_at`, and `state`.
2. If `next_run_at` is in the past but `last_run_at` is still `null`, do not tell user it is running. Say scheduler has not picked it yet and either trigger again or run first iteration manually.
3. For high-stakes/autonomous loops, perform one manual iteration in-session: inspect status, build, implement one safe improvement, commit/push/deploy/verify, then leave cron to continue.
4. Use schedule strings like `every 30m` rather than ambiguous one-shot wording. Confirm list output says `every 30m`, not `once in 30m`.
5. Ask cron jobs to write a durable progress artifact (for example `reports/productionization-loop.html`) so output can be inspected even if chat delivery lags.

## Pitfalls

- Do not equate `cronjob(action="run") success` with completed execution; it may only schedule/queue a run.
- Slow model: keep prompts narrow and batch small; treat ds2api as background search, not blocking brain.
- Search-backed answers can hallucinate; require URLs and verify sampled URLs.
- Do not reveal internal model/provider details in customer-facing copy. Say `AI-assisted research` or `web-backed research`, not `9Router/ds2api`.
- Keep secrets in `~/AppData/Local/hermes/secrets/user.env`; never under `D:/code`.
