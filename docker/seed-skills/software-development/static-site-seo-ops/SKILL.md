---
name: static-site-seo-ops
description: "Operate static SEO content sites: improve on-page SEO, validate/generated content, deploy via git, and set up daily content-update jobs."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [seo, static-sites, content-ops, deployment, cron]
    related_skills: [writing-plans, requesting-code-review, github-pr-workflow]
---

# Static Site SEO Operations

## When to Use

Use when working on a static SEO/content site where tasks include:
- improving article quality, on-page SEO, schema, CTAs, footer trust signals
- fixing generated content artifacts (bad headings, thin content, wrong CTA links)
- validating/building static content
- deploying by commit/push
- creating scheduled jobs to update news/articles for a target keyword

## Workflow

1. **Identify repo and protected repos**
   - Confirm exact repo path and any repos/paths the user says to leave alone.
   - Do not clean or deploy unrelated repos.

2. **Read site structure before editing**
   - Inspect `package.json`, generator scripts, validator scripts, `content/*.json`, article templates, and homepage.
   - If site has generated HTML, patch source generator first; then regenerate rendered files.

3. **SEO/AI content quality checks**
   - Use Vietnamese with full diacritics for Vietnamese sites.
   - Avoid no-accent headings like `Tom tat tot nhat` in visible content.
   - Keep top answer blocks easy for AI/search engines to extract: concise summary, key takeaway, audience, action steps, risks, sources/date checked.
   - For legal/tax/policy content, prefer official sources and keep caveat: content is educational and does not replace specialist advice.

4. **CTA, monetization stance, and identity accuracy**
   - First determine the site's role: traffic/knowledge hub, product landing page, lead-gen page, portfolio, or app docs. Do not assume conversion/sales is desired just because SEO is involved.
   - If user says a site is a traffic asset or “hút view/hút traffic”, remove monetization surfaces from that domain: sales CTAs, audit offers, service packages/prices, lead capture, consultation prompts, and product promotion.
   - Replace conversion CTAs with traffic/internal-navigation CTAs: `Đọc bài liên quan`, `Xem checklist`, `Quay lại danh sách bài`, `Đọc tiếp`, or section anchors.
   - Update CTA targets exactly as user requests.
   - Distinguish product/site links from author/profile links. If user says a domain is author info, do not label it as a product/demo.
   - Ensure homepage, article templates, rendered articles, JSON indexes, schema, validators, cron prompts, and stale public pages (reports/old landing pages) agree.

5. **Trust/footer signals**
   - For Vietnamese business/content sites, footer can include three tiers:
     - brand/contact
     - legal/trust: DMCA and Bộ Công Thương placeholders if real links/icons are pending
     - ecosystem/author/resources
   - Use placeholders clearly (`#dmca-protection-link`, `#bo-cong-thuong-link`) until official verification links exist.

6. **Validate before deploy**
   - Run project validation/build commands from `package.json`, commonly:
     ```bash
     npm run content:generate
     npm run content:validate
     npm run build
     ```
   - If validation fails after editing rendered files, regenerate from source templates instead of hand-fixing every generated page.

7. **Deploy via git**
   - Check `git status --short`, remote, and branch.
   - Commit only intended files.
   - Push to deploy branch when build passes.
   - Report commit hash, validation result, and whether push succeeded.

8. **Daily SEO cron jobs**
   - Use a high-quality prompt; do not blindly run generators.
   - Require one safe action per day: update news JSON, create one sourced long-tail article, or improve one weak article.
   - Require validation/build before commit/push.
   - Require no commit if content quality is weak or validation fails.
   - Include target keyword, source policy, CTA/monetization rules, protected domains/repos, and output report format in cron prompt.
   - When a user corrects the site's business intent (e.g. “not monetized here”), immediately update existing cron prompt(s) so future automated runs do not reintroduce forbidden CTAs.

9. **Post-edit anti-regression scan**
   - Search rendered HTML, content JSON, generators, and old public pages for stale sales/identity strings before deploy. Example patterns: `Nhận audit`, `Gửi website`, `Gửi email`, `SEO Machine`, `Gói dịch vụ`, price ranges, and `mailto:<email>?subject=`.
   - If public stale pages exist (old reports/landing pages), convert to noindex/redirect or rewrite them to match the site role; do not leave them reachable with conflicting CTAs.

## References

- `references/doanhnghiep1nguoi-traffic-hub-2026-05-10.md` captures a concrete traffic-hub correction and anti-regression scan patterns.

## Pitfalls

- Do not label an author/personal domain as a product or SEO Machine unless user explicitly says so.
- Do not preserve no-accent Vietnamese headings in visible content just because generator originally used them.
- Do not deploy when validator fails; fix generator/templates then regenerate.
- Do not commit broad unrelated changes like `.gitignore` unless intentionally reviewed.
- Do not create legal/tax claims from press or model memory. Use official references or phrase as general caution.

## Verification Checklist

- [ ] No old CTA email/domain remains in content/search results.
- [ ] Visible Vietnamese headings have diacritics.
- [ ] JSON-LD parses and matches visible content.
- [ ] `content/articles.json` and rendered article pages agree.
- [ ] `sitemap.xml`, `robots.txt`, and `llms.txt` are refreshed if article list changes.
- [ ] Build/validation passes.
- [ ] Commit and push completed, or skipped with clear reason.
