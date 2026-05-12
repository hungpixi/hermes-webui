# Organic Lead Engine pricing and compute-mode pattern

Use this pattern when turning an AI-agent SEO/lead-gen dashboard into a sellable Vietnamese SaaS.

## Core positioning

Do not sell cheap AI tokens or "unlimited content". Sell workflow, reports, automation, and implementation leverage.

Customer-facing wording:

- "Dùng AI của chúng tôi hoặc kết nối AI router/API riêng của bạn."
- "Không bị khóa compute: dùng Hosted AI, OpenAI-compatible endpoint, 9Router/OpenRouter, hoặc provider riêng."

Avoid beginner-facing copy that over-explains internal provider names. Put Custom Router behind an advanced/pro setting.

## Compute modes

### Mode A — Organic Hosted AI

For normal users: SME owners, solo founders, freelancers, creators, local service businesses.

The product controls model routing, quotas, retries, cache, fallback, logging, and usage tracking. Charge with credits and workspace/report limits.

### Mode B — Custom Router / BYOK

For technical users, agencies, and higher-volume users. Let them provide an OpenAI-compatible endpoint such as 9Router/OpenRouter/custom API.

Suggested settings fields:

```env
CUSTOM_AI_BASE_URL=
CUSTOM_AI_API_KEY=
CUSTOM_AI_MODEL=
CUSTOM_AI_PROVIDER_NAME=
```

Important network constraint: if the app is hosted on a server, it cannot call `localhost` on the user's laptop. BYO 9Router needs one of:

1. User deploys 9Router/public API on their own VPS.
2. Vendor hosts a dedicated 9Router/router instance for that customer.
3. Future local companion app.

Start with 1 and 2.

## Pricing ladder

Suggested Vietnamese market ladder:

| Plan | Price | Compute | Custom Router | Purpose |
|---|---:|---|---|---|
| Free | 0đ | 5 articles/reports per day | No | trial, report taste |
| Starter | 99k/mo | 50 credits/mo | No | beginners, SMEs |
| Pro | 299k/mo | 200 hosted credits/mo | Yes | freelancer/solo founder |
| Studio | 499k/mo | 500 hosted credits/mo | Yes | small agency |
| Infra/Custom | from 2M setup | custom | hosted/private router | high-volume/client workflow |

Free should have watermark and no image generation. Custom Router should start at Pro, not Free.

## Limits and anti-abuse

Never promise unlimited content. Use:

- credits
- queue
- rate limit
- daily cap
- fair use policy
- cache
- retry/fallback
- abuse detection

Even with Custom Router, enforce workspace limits:

- project count
- report storage count
- concurrent jobs
- batch URL limits
- storage size
- automation frequency
- crawler/export limits
- team seats

Free anti-abuse product policy:

- 5 articles/reports per day
- no image generation
- watermark
- IP checks
- browser fingerprint checks
- Google/Facebook login or social verification
- email verification
- velocity checks for multi-account abuse

When implementing anti-abuse UI/copy, include privacy-friendly wording. Do not implement invasive tracking silently.

## Good product workflows

Sell controlled SEO pipeline, not spam content generation:

```text
Topic research -> keyword clustering -> topical map -> content brief -> article draft -> entity/schema check -> internal links -> human review -> export
```

Prefer high-volume usage for:

- briefs
- outlines
- audits
- prospect reports
- client proposals

Avoid defaulting to:

- auto-publish at scale
- thin content
- spam outreach

## Revenue thesis

SaaS 99k-499k/mo opens relationships and validates demand. Private setup, white-label, hosted router, and content/report factory setup create larger cash flow.

Examples:

- Setup 9Router + Organic Lead workflow: 2-5M one-time
- Host/private router maintenance: 500k-2M/mo
- Private SEO content/report factory: 3-10M setup
- Agency white-label: 5-20M setup

## Implementation checklist

- Add `/pricing` page with Vietnamese copy and plan limits.
- Add Settings > AI Provider with Hosted AI vs Custom Router.
- Add compute source selector in agent runner.
- Store report metadata: `compute_source`, `model`, `skill`, `credits_used`, `status`.
- Expose pricing/compute rules in dashboard API if useful.
- Update autonomous sellability/improvement cron prompt with these constraints.
