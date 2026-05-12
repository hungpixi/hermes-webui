# Reference: SEO Machine BA Pattern

Session pattern for an AI SEO SaaS/product:

- User rejected dashboard-heavy landing: page cropped/overflowed and failed sales-layout purpose.
- Correct approach: write BA first, then rebuild UI.
- Public product should be positioned as AI SEO content operating system for small teams, not as internal gateway/model stack.
- Landing language should be 100% Vietnamese when targeting Vietnamese customers. Terms like SEO, WordPress, API are acceptable.
- Landing should sell outcome and drive CTA to demo/dashboard; dashboard contains dense pipeline.

## Landing structure

```txt
Hero: outcome + target user + CTA + compact dashboard mockup
Problem: keyword chaos, AI slop risk, review bottleneck, no measurement
Workflow: import -> cluster -> brief -> draft -> review -> publish -> monitor
Dashboard preview: compact cards only
Use cases: local service, ecommerce, SaaS, affiliate, agency
Pricing/service: free audit, setup pilot, managed publishing, agency scale
FAQ: Google/AI risk, WordPress, API keys, timeline, review, GSC/GA4
Final CTA: audit request form
```

## Dashboard IA

```txt
/app
/app/projects
/app/keywords
/app/briefs
/app/articles
/app/review
/app/publish
/app/analytics
/app/settings
```

## AI provider wording

Customer-facing:

```txt
OpenAI-compatible API
Custom API Base URL
Bring your own AI key
Multi-provider support
Provider fallback
Cost controls
Model routing by task type
```

Avoid in marketing:

```txt
9Router
cx/
Codex auth/free
internal provider route names
```

## High-volume content guardrail

For “hundreds of posts/week” products, require:

- clustering/deduplication
- approval gate
- review score/risk flags
- draft-first WordPress push
- scheduling
- GSC/GA4 feedback loop

## WordPress plugin role

Plugin is bridge:

- connects WP site to SaaS
- syncs site metadata/categories/tags/posts
- pulls approved content packages
- pushes drafts/scheduled posts
- writes Yoast/RankMath fields
- logs failures

Not everything should live inside WordPress; dashboard remains control plane.
