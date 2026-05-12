# GitHub portfolio monetization audit notes

When user asks “vọc GitHub của tôi xem có gì đáng làm ra tiền”, treat as a portfolio/business audit, not only repo cloning.

## Durable workflow

1. Use `gh` first for remote reads when authenticated.
2. Export repo metadata JSON with `gh repo list <owner> --limit 200 --json name,description,isPrivate,isFork,updatedAt,primaryLanguage,stargazerCount,url`.
3. Score candidates by buyer pain and speed to paid pilot:
   - B2B niche with budget (BIM/Revit, SEO ops, lead-gen, AI infra) > generic agent demos.
   - Existing working code/docs/demo > concept-only.
   - Non-fork and recently updated > stale fork.
   - Productized service path > SaaS build-first when user wants money fast.
4. Clone top candidates into requested workspace and inspect project maps before ranking.
5. Present offers, buyer segments, price bands, risks, and concrete next actions.

## Example findings from one portfolio

Repos cloned to `D:/code` for review:

- `revit-mcp` — Revit/BIM MCP bridge with Node + C# plugin. Highest B2B ticket potential. Offer: Revit AI Automation Pilot. Pilot VN 10–30m VND; international $1k–$5k setup; retainer $300–$2k/mo. Needs working Revit demo.
- `seo-machine-publisher` + `doanhnghiep1nguoi` — SEO Machine workspace + static content hub with daily content builder. Fastest cash. Offer: AI SEO Publishing Machine for solo founders/SMEs. Setup 5–20m VND or $500+; monthly 3–15m VND or $300–$1500/mo.
- `social-lead-gen` — FB group crawler/classifier/outreach pipeline. Good lead-gen service, but platform/compliance risk.
- `9router` — Dokploy wrapper for AI gateway. Good add-on service for AI infra teams, smaller market.
- `OneManCompany` — internal operating system/templates; use to support service delivery, not first product.
- `agent-armory` — developer-facing MCP tool discovery; interesting but harder to monetize early.

Recommended strategy:

- Run two tracks for 14 days: SEO Machine for fastest cash, Revit MCP for high-ticket moat.
- Build two short landing pages, two 2–3 minute demos, 100 SEO prospects, 50 Revit/BIM prospects.
- Outreach scripts should offer a free mini-audit/demo tied to a real workflow.

## Progress communication pitfall

For long audits, visible progress matters. Report each milestone briefly (metadata fetched, candidates scored, clone started/done, inspection started/done), then continue executing. Do not pause after a progress update unless user input is required.