---
name: static-site-seo-content
description: "Use when improving static HTML/JSON content sites for SEO, AI-answer visibility, CTA conversion, schema markup, and trust/compliance footer elements."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [seo, static-site, content, schema, conversion, ai-seo]
    related_skills: [writing-plans]
---

# Static Site SEO Content

## When to use

Use for static sites where content lives in HTML, JSON, markdown, or generator scripts and user asks to improve:
- SEO / AI SEO / AEO / GEO readiness.
- Thin or poor-quality articles.
- Vietnamese content quality, accents, headings, and readability.
- CTA placement, contact email, internal links, footer trust signals.
- JSON-LD schema for homepage or article pages.
- Generated content systems where source config and rendered pages can diverge.

## Workflow

1. **Inspect source of truth first**
   - Find generator scripts, config files, content indexes, rendered pages.
   - Typical files: `scripts/generate*.mjs`, `content/site.config.json`, `content/articles.json`, `content/articles/*.html`, `index.html`, `llms.txt`, `robots.txt`, `sitemap.xml`.
   - If rendered pages are generated, patch generator first, then rendered output or regenerate.

2. **Plan user-facing changes before editing**
   - State short plan: headings, CTA, schema, footer, validation.
   - Keep plan concrete and path-aware.

3. **Fix language quality at template level**
   - Avoid no-accent Vietnamese in visible headings.
   - Replace generator labels and validation requirements, not only rendered HTML.
   - Preserve slug/id compatibility if URLs depend on ASCII ids.

4. **Update CTA globally**
   - Search old email/domain across homepage, articles, JSON indexes, config, validation scripts, docs.
   - Patch all user-facing CTAs and metadata so future generated pages keep correct values.

5. **Improve AI/SEO structure without making page robotic**
   - Start with reader tension or lived situation before dry answer blocks when topic is decision-heavy.
   - Good opening pattern: H1 → human intro → `Câu trả lời nhanh` → quick classification table/box → TOC.
   - Avoid stacking many generic H2s at the top (`Tóm tắt tốt nhất`, `Điểm quan trọng nhất`, `Áp dụng cho ai`, `Việc cần làm ngay`, `Rủi ro`, `Nguồn`) unless legacy validator requires them. If required, keep labels but make them compact and meaningful.
- Add stance sentence and decision classification, not only summary boxes.
- Add at least one case story or concrete example for pillar/YMYL/resource pages.
- Use the Doanh Nghiệp 1 Người business-registration pillar as quality benchmark: human opening, fast answer, quick classification table, TOC, main body, reader-oriented table heading, concrete case story, deep sections with example/risk/action, professional trust note.
- Main published articles must be 2,000+ words unless user explicitly requests glossary/micro page.
- HTML structure should match gold standard: `<div class="kicker">` → single `<h1>` → human intro paragraphs → hero figure if available → fast answer box → quick classification table → caveat/source note → `<nav class="toc">` → main action table → case story → deep sections → checklist/FAQ/trust/sources/internal links → JSON-LD.
- If legacy validators force old headings, prefer updating validator. If not possible, keep compatibility labels non-disruptive; do not expose six generic H2 blocks at top.
   - Keep headings natural and accented for humans; ids may remain ASCII.
   - Use internal links as topic map, not keyword stuffing.

6. **Schema markup**
   - Prefer JSON-LD.
   - Homepage: `WebSite`, `Organization`.
   - Articles: `Article` or `NewsArticle`, `FAQPage`, `BreadcrumbList`, `Organization`, `WebSite`, `ItemList` for related content.
   - Add `Organization.contactPoint` and `sameAs` when user supplies contact/domain.
   - Schema must match visible content.

7. **Footer trust layer**
   - For small business/content hubs, footer can use 3 columns:
     1. Brand + description + contact.
     2. Legal/trust: disclaimer, DMCA, Bộ Công Thương placeholder/icon link.
     3. Ecosystem: homepage, content index, product/service domain.
   - Use placeholder anchors when official verification links are not ready; label clearly for later replacement.

8. **Validation and asset safety**
   - Update validation scripts when requirements change.
   - Search for stale strings after patching: old email, old domains, no-accent labels, old CTA text.
   - Before commit, read the first 30–40 lines of any generated article HTML to catch malformed escaped attributes such as `class=\"note\"`.
   - Mandatory local visual gate before push: start/use local server, open changed article/homepage on localhost in browser, verify CSS is loaded, layout is not broken, hero image loads, tables are readable, footer/nav render, and no severe console errors. Build pass alone is not enough.
   - If local visual check cannot be done, do not push; report blocker.
   - Verify every referenced CSS/image asset exists and is staged. Missing CSS can pass content validation but break live UI.
   - If `.gitignore` ignores an intended shared asset such as `assets/styles.css`, use `git add -f` and mention why.
   - If terminal is not available, use file search/read/write tools and report that build was not run.
   - For live verification on cached static sites, try canonical URL variants only when appropriate (`/slug`, `/slug/`, and cache-busted query like `?v=<commit>`). Report if one variant still serves stale cache while another shows latest content.

## Pitfalls

- Do not only patch rendered HTML when generator will overwrite it.
- Do not leave validation scripts checking old email or old no-accent headings.
- Do not replace technical URLs blindly; preserve API/router domains if they are not public SEO CTAs.
- Do not claim legal compliance from placeholder DMCA/Bộ Công Thương links; say links/icons need official verification later.
- Do not over-optimize with repeated keyword phrases. Use helpful, source-backed blocks and real internal links.
- Do not push a generated article after build alone; inspect HTML and asset references. CSS/asset mistakes can pass validators and still create visibly broken pages.
- Do not claim a queued cron/manual job ran until job state or repository changes prove it. If user asks for immediate output and scheduler has not executed, perform the work manually in-session.

## Useful searches

Search stale no-accent Vietnamese headings:

```regex
Tom tat tot nhat|Diem quan trong nhat|Ap dung cho ai|Viec can lam ngay|Rui ro neu hieu sai|Nguon (&amp;|&) ngay kiem tra
```

Search stale CTA/domain values:

```regex
old@example\.com|old-subdomain\.example\.com
```

## Support files

- `references/doanhnghiep1nguoi-seo-cleanup.md` records a concrete static-site cleanup pattern from a Vietnamese solo-founder content hub.
- `references/doanhnghiep1nguoi-batch-content-lessons.md` captures batch article production pitfalls: queued cron not equal completed work, generated HTML escaped attributes, ignored CSS assets, and SVG fallback heroes.
- `references/static-html-article-library-upgrade.md` captures batch upgrade workflow for static article libraries: cross-check HTML files vs JSON index, normalize thin pages, update homepage counts/cards, and validate.
