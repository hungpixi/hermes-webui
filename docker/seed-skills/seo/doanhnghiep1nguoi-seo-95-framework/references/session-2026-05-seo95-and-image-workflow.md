# Session notes: SEO 95 + image workflow for Doanh Nghiệp 1 Người

## Durable lessons
- User corrected earlier content updates: 7–8 minute posts were considered artificial/thin for this site. Default target now: each published article should be 20+ minutes unless user explicitly asks otherwise.
- User asked to load all SEO skills and make local repo rules automatic. Repo-level `AGENTS.md` should include all relevant SEO, YMYL, ecommerce/resource, AI schema, Yoast audit, 9Router image, and SEO95 framework skills.
- User emphasized: content must have real update value, not just `updatedAt`; H2s must not repeat generic stacks across articles.
- Workflow correction: when both content and images are needed, repair content/schema/audit first; insert images after. Do not wait for slow image batch to continue content audit.

## Implementation pattern proven in session
1. Create/update local `AGENTS.md` with required skills and validation commands.
2. Add `scripts/seo_95_audit.py` to score articles against framework.
3. Add GitHub Actions workflow `.github/workflows/seo-95-audit.yml` to run JSON parse, `node --check server.js`, and SEO 95 audit.
4. For low-scoring articles, add:
   - 10-row classification table with official-source column.
   - 6 warning signals, each with description, real example, action, source.
   - Case story with timeline, channel (Zalo/Facebook), money range, invoice/contract turning point.
   - Checklist 30 minutes before decision.
   - Descriptive internal links back to pillar `khi-nao-can-dang-ky-kinh-doanh` and cluster articles.
   - FAQ expanded to at least 12 questions when pillar/YMYL/resource article.
   - JSON-LD `@graph` containing Organization, Person, BreadcrumbList, Article, FAQPage.
5. Run audit with a temporary image-relaxed threshold/content interpretation if images are still pending; full CI should enforce >=3 images after insertion.

## 9Router image workflow note
- Use `image-generation-9router` and Codex image model such as `cx/gpt-5.4-image` when available.
- Generate 3 useful images per article: hero/OG, process/workflow, checklist/decision visual.
- Save batch plan to something like `content/article-image-plan.json`; save outputs under `assets/`.
- Then update HTML figures, `content/articles.json` hero fields, and JSON-LD image fields together.
