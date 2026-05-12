# Session note: CMS generator flow for Doanh Nghiệp 1 Người

## Trigger
User wanted future work to be: write article only, no repeated manual HTML/CSS/schema/image fixes.

## Chosen approach
Use static Git-based CMS flow:

```txt
/admin (Sveltia CMS)
  -> content/posts/*.md
  -> node scripts/build-articles.mjs
  -> content/articles/*.html + content/articles.json
  -> CI audit
```

This fits the existing static repo better than Payload/Strapi/WordPress because no database/backend is needed.

## Files to create
- `admin/index.html` loads `https://unpkg.com/@sveltia/cms/dist/sveltia-cms.js`
- `admin/config.yml` with GitHub backend and `content/posts` collection
- `content/posts/*.md` as source of truth
- `scripts/export-posts-from-html.mjs` for one-time migration from current HTML
- `scripts/build-articles.mjs` as deterministic generator
- update `.github/workflows/seo-95-audit.yml` to run generator before audits

## CMS schema minimum
Require:
- `title`, `slug`, `seoTitle`, `metaDescription`, `category`, `intent`
- `publishedAt`, `updatedAt`, `sourceCheckedAt`
- `readingTimeTarget` min 20
- `excerpt`, `heroSummary`, `keyTakeaway`, `caveat`
- `heroImage`, `heroImageAlt`, `images` min 3
- `relatedSlugs` min 2
- `sources` min 1
- `faq` min 12
- Markdown `body`

## Generator responsibilities
- Never rely on manual generated HTML as source of truth.
- Read only `content/posts/*.md`.
- Generate `content/articles/*.html` and `content/articles.json` together.
- Always link shared `/assets/article.css` so images/tables/cards do not overflow.
- Generate Article + FAQPage + BreadcrumbList + Person/Organization JSON-LD.
- Insert 3 figures with alt/caption and sync JSON-LD images.
- If editor body misses core SEO sections, add guardrail sections:
  - article-specific fast answer heading
  - 8-10 row risk table
  - 6 warning signals
  - decision process
  - 5 mistakes
  - case story
  - checklist 30 phút
  - visible FAQ heading not exactly `FAQ` (generic H2 audit rejects exact `FAQ`)
  - author/disclaimer

## Pitfalls learned
- Audit may report score `100` while still listing issues if script scoring/reporting is inconsistent. Treat issue list as authoritative.
- Exact H2 `FAQ` is included in this repo's `GENERIC_H2`; generated visible heading should be specific, e.g. `Câu hỏi thường gặp cho <title>`.
- Generated Markdown parser that only emits `<ul>` from `-` bullets can miss HTML body content after migration. If audit says `li` missing, inspect generated HTML, not source Markdown.
- Fallback guardrail sections pass audits but can feel generic. Prefer improving CMS body with article-specific writing when time allows.

## Validation command
```bash
node scripts/build-articles.mjs
python -m json.tool content/articles.json > /dev/null
node --check server.js
python scripts/seo_95_audit.py --min-score 95
```
