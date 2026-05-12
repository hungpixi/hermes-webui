---
name: ai-gateway-9router
description: Use 9Router as local/remote AI gateway for chat, web search/fetch, image, TTS, STT, embeddings. Covers setup, model discovery, provider quirks, and research workflow.
---

# 9Router AI Gateway

Use when user mentions 9Router, `NINEROUTER_URL`, AI gateway, OpenAI-compatible local router, or asks to use 9Router for chat/search/fetch/model capabilities.

## Core setup

```bash
export NINEROUTER_URL="http://localhost:20128"   # or tunnel/VPS URL
export NINEROUTER_KEY="sk-..."                   # if API auth enabled
```

For user's hosted 9Router, default remote base is:

```bash
export NINEROUTER_URL="https://9router.phamphunguyenhung.com"
```

For app code, support OpenAI-style aliases when convenient:

```text
ROUTER_BASE_URL=https://9router.phamphunguyenhung.com
ROUTER_API_KEY=<secret>
# fallback aliases: NINEROUTER_API_KEY or OPENAI_API_KEY
```

OpenAI-compatible chat endpoint:

```text
$NINEROUTER_URL/v1/chat/completions
```

Verify:

```bash
curl "$NINEROUTER_URL/api/health" || curl "$NINEROUTER_URL/health"
```

Expected:

```json
{"ok":true}
```

Do not persist user API keys in memory or skill files. Use env vars or one-shot command env only. If user pastes key in chat, avoid repeating it and warn that they may rotate it if logs are shared.

## Model discovery

```bash
curl "$NINEROUTER_URL/v1/models"                  # chat/LLM
curl "$NINEROUTER_URL/v1/models/image"            # image generation
curl "$NINEROUTER_URL/v1/models/tts"              # text-to-speech
curl "$NINEROUTER_URL/v1/models/embedding"        # embeddings
curl "$NINEROUTER_URL/v1/models/web"              # web search/fetch
curl "$NINEROUTER_URL/v1/models/stt"              # speech-to-text
curl "$NINEROUTER_URL/v1/models/image-to-text"    # vision
```

Use returned `data[].id` for normal model fields, but see web-search provider quirk below.

## Capability skill docs

Session reference: `references/image-generation-logo-workflow.md` captures a proven 9Router image-generation workflow for SaaS logos, including model discovery, prompt structure, b64/url saving, Next.js asset integration, and verification.

Fetch upstream skill docs when needed:on workflow for SaaS logos, including model discovery, prompt structure, b64/url saving, Next.js asset integration, and verification.

Fetch upstream skill docs when needed:

- Entry: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router/SKILL.md
- Chat: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-chat/SKILL.md
- Image: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-image/SKILL.md
- TTS: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-tts/SKILL.md
- STT: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-stt/SKILL.md
- Embeddings: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-embeddings/SKILL.md
- Web search: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-web-search/SKILL.md
- Web fetch: https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-web-fetch/SKILL.md

## Web search workflow

Preferred research pipeline:

```text
/v1/search -> /v1/web/fetch -> /v1/chat/completions synthesize
```

Search providers commonly available:

- `tavily` / `tavily/search`
- `brave` / `brave/search`
- `exa` / `exa/search`

### Provider field quirk

Some 9Router setups list web models as IDs like `tavily/search`, `brave/search`, `exa/search`, but `/v1/search` may reject `model:"tavily/search"` with:

```json
{"error":{"message":"Unknown provider: tavily/search","type":"invalid_request_error","code":"bad_request"}}
```

Fix: call `/v1/search` with bare provider names:

```json
{"provider":"tavily","query":"...","max_results":8}
{"provider":"brave","query":"...","max_results":8}
{"provider":"exa","query":"...","max_results":8}
```

Python probe:

```python
import os, json, urllib.request
base = os.environ.get("NINEROUTER_URL", "http://localhost:20128").rstrip("/")
key = os.environ["NINEROUTER_KEY"]
headers = {"Content-Type":"application/json", "Authorization":"Bearer " + key}
payload = {"provider":"brave", "query":"one-person company AI solopreneur skills 2025", "max_results":8}
req = urllib.request.Request(base + "/v1/search", data=json.dumps(payload).encode(), headers=headers, method="POST")
print(urllib.request.urlopen(req, timeout=45).read().decode())
```

Provider selection:

- `brave`: broad web results, decent snippets, good default.
- `tavily`: strong snippets/answers, good quick summaries, can include social/noisy sources.
- `exa`: semantic discovery, often relevant titles, may return sparse snippets; fetch pages before judging.

## Chat is not web search

`/v1/chat/completions` models may not have browsing/grounding even if they are strong reasoning models. If asked to “search with AI model,” test explicitly with prompt requiring current URLs and instruction to say `CANNOT_BROWSE` if no browse. In observed 9Router chat routes, models returned `CANNOT_BROWSE`; use `/v1/search` for real retrieval, then chat for synthesis.

For Codex-login/free routes, prefer `cx/` chat models when user asks for Codex-backed reasoning/code/synthesis. Discover with:

```bash
curl "$NINEROUTER_URL/v1/models" | jq -r '.data[].id' | grep '^cx/'
```

Observed examples include `cx/gpt-5.5`, `cx/gpt-5.2`, `cx/gpt-5.3-codex`, and reasoning tiers like `cx/gpt-5.3-codex-xhigh`. These are useful after retrieval, but still do not replace `/v1/search` unless a future setup explicitly exposes browsing/grounding.

## Image generation notes

Use `/v1/images/generations` for logo/product image work. Discover models with `/v1/models/image`; check `/v1/models/info?id=<model>` for model-specific options. JSON `response_format:"b64_json"` worked for saving a PNG from `cx/gpt-5.4-image`; upstream docs also support query `?response_format=binary` for raw bytes. For logo generation, ask for no text unless a wordmark is explicitly wanted, then inspect with vision because model output can be visually strong but too busy at favicon size.

## Product-facing provider abstraction

When building customer-facing SaaS/plugin UI on top of 9Router, do not expose internal route names like `9Router`, `cx/`, Codex auth, or free/internal provider details in marketing copy or customer settings unless user explicitly asks for internal/admin docs.

Customer-facing wording should be generic and durable:

- `OpenAI-compatible API`
- `Custom API Base URL`
- `Bring your own AI key`
- `Multi-provider support`
- `Provider fallback`
- `Cost controls per workspace`
- `Model routing by task type`

Internal routing can still use 9Router and `cx/` for synthesis/reasoning/code. Public product UI should present provider profiles and task roles (`fast model`, `quality model`, `review model`, `embedding model`) rather than internal provider secrets.

## Long research progress rule

For multi-step search/research/debug tasks, keep user informed while working:

1. Announce plan and current step before tool calls.
2. After each major probe, report short result: OK/error and next step.
3. Do not report progress then stop unless waiting for user input.
4. If credentials/config missing, say exactly what is missing and what command/env var fixes it.
5. Continue executing once user provides missing input.

This matters for users who expect visible progress during long tool runs.

## Product/SaaS agent API pattern

For Next.js/Dokploy dashboards that need server-side agent runs, report saving, and JSON/HTML exports, see `references/nextjs-agent-report-api.md`. Key durable rules: discover chat models from `/v1/models` before choosing `AGENT_MODEL`, keep secrets server-side, add fallback report generation, and make sure `NINEROUTER_URL` is reachable from the deployed container, not only from the local laptop.

For fast MVPs that need AI-backed actions before real backend/AI is finished, see `references/nextjs-mvp-fallback-api-pattern.md`. Pattern: server-only OpenAI-compatible env aliases, JSON-shaped `/chat/completions`, and deterministic demo fallback on missing key/API error so UX still works.

## Common errors

- `401 Missing API key` -> set `NINEROUTER_KEY` or disable auth in router config.
- `400 Unknown provider: <x/search>` on `/v1/search` -> retry with bare provider `<x>`.
- `400 Invalid model format` -> rediscover models and use correct capability endpoint/model.
- `503 All accounts unavailable` -> wait for retry or add provider account.
