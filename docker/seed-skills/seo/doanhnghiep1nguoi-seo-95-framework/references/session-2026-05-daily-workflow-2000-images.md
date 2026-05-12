# Daily workflow: 2000+ words, 3 images, SEO gate

Session learning from auditing old `Daily Content Builder` workflow.

## Problems found
- Old prompt asked for `1800+ Vietnamese words`, below user expectation.
- `validate-content.mjs` did not fail on missing 3 images.
- `npm run build` did not run CMS generator or SEO 95 audit.
- Daily workflow auto-commit omitted `content/posts/*.md`, `assets/*.png`, and `assets/uploads/*`.
- Generated articles needed shared `assets/article.css` and `og:image` in head.

## Durable fix pattern
1. Set `content/site.config.json` minimum article word count to `2000` or higher.
2. In AI writer prompt, request `2200+ Vietnamese words` to leave margin.
3. Require 3 image objects: `hero`, `process`, `checklist`; generator should render 3 `<figure class="article-visual ...">` blocks.
4. Validate each published article for:
   - visible word count >=2000
   - `<img>` count >=3
   - `rel="canonical"`
   - meta description
   - JSON-LD
   - `og:image`
   - `/assets/article.css` or equivalent safe image CSS
   - source section, CTA, internal links
5. Build command should run generator before validation:
   ```bash
   node --check server.js
   node --check scripts/daily-content-builder.mjs
   node --check scripts/build-articles.mjs
   node scripts/build-articles.mjs
   node scripts/validate-content.mjs
   python scripts/seo_95_audit.py --min-score 95
   ```
6. Daily auto-commit file pattern should include:
   ```txt
   content/articles.json content/articles/*.html content/posts/*.md assets/*.png assets/uploads/* sitemap.xml robots.txt llms.txt
   ```

## Reporting rule
Report current article count, min visible word count, min image count, and exact issues list. Do not call workflow good because it has a high score if these gates are missing.
