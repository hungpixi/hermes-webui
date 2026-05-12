---
name: ai-schema-geo-seo-vi
description: "AI-era SEO skill for long structured content, JSON-LD schema, AI Overviews, AEO/GEO, llms.txt, and source-backed answer blocks."
---

# AI Schema & GEO SEO VI

Use when creating long-form content meant for Google AI Overviews, featured snippets, Perplexity/Gemini/Copilot-like answers, and schema-rich pages.

## Principles
- People-first content. Search engines and AI systems are secondary readers.
- Answer first, explain later.
- Make entities explicit: who, what, when, where, why, how, source.
- Use structured data only for content visible on the page.
- Long content must have structure, not filler.

## Long article structure
Use structure that AI can extract and humans want to keep reading.
1. H1 with natural primary entity/query.
2. Human opening: show the reader's real tension in 2–3 short paragraphs. Do not start every serious article with a dry checklist line.
3. 40–100 word direct answer or `Câu trả lời nhanh`.
4. Quick classification table/box if intent involves decisions: low-risk / needs checking / expert-required.
5. Trust box: updated date, legal/editorial caveat if needed, source priority. Keep it compact.
6. Table of contents after reader is oriented.
7. Definitions and synonyms.
8. Comparison table if intent includes choices.
9. Step-by-step or checklist if intent includes action.
10. Risks/misunderstandings.
11. Case story or examples from target market. Pillar/YMYL pages should include at least one concrete mini-case.
12. FAQ with concise answers.
13. Sources and date checked.
14. Related reading/internal links.

Avoid top-heavy module stacks. If page begins with many H2s like summary, key point, audience, actions, risks, sources before core content, it may pass SEO but feel machine-made. Merge those into one compact answer/trust box unless a project validator forces separate labels.

## Useful visual assets for AI/SEO pages
- Add visuals only when they help the answer: decision trees, workflow loops, comparison diagrams, checklists, process maps. Avoid decorative stock art.
- If 9Router is available, use image generation endpoint with `NINEROUTER_URL` and `NINEROUTER_KEY`; model examples seen: `cx/gpt-5.4-image`, `gemini/gemini-3-pro-image-preview`.
- Save assets under `assets/` with descriptive slugs.
- Vision-check generated images before deploy, especially Vietnamese text. Regenerate on typos; fallback to hand-authored SVG for exact labels.
- Add `<figure>`, meaningful `alt`, caption, `og:image`, and `image` in Article JSON-LD.
- In Docker/static deployments, verify asset directories are copied and server MIME types include `.png`, `.svg`, `.webp`.
- See `references/9router-image-notes.md` for 9Router image generation commands and SEO image checklist.

## Answer extraction blocks
Use blocks AI can quote, but keep page human:
- “Câu trả lời ngắn”
- “Tóm tắt nhanh”
- “Bảng phân loại nhanh”
- “Bảng so sánh”
- “Checklist”
- “Điểm cần nhớ”
- “Nguồn & ngày kiểm tra”

Do not overuse extraction blocks. Good pattern: human opening → direct answer → one classification table → main article. Bad pattern: six summary H2s before giving context.

## Schema workflow
1. Choose page type:
   - `Article` or `BlogPosting` for articles.
   - `FAQPage` only if FAQ visible.
   - `BreadcrumbList` if breadcrumb visible.
   - `Organization` and `Person` for identity/trust.
   - `Course`, `Product`, `SoftwareApplication`, `LocalBusiness` only when true.
2. Build JSON-LD with stable canonical URL.
3. Include `datePublished`, `dateModified`, `author`, `publisher`, `mainEntityOfPage`.
4. For FAQ, map exact visible questions/answers.
5. Validate with Rich Results Test and Schema Markup Validator.
6. Keep schema in generator/template, not hand-edited stale HTML.

## GEO/AEO checks
- Main answer appears before deep explanation.
- Each H2 can stand alone as a user question/intent.
- Claims have sources.
- Entities have consistent names and synonyms.
- Tables summarize differences.
- FAQ answers are short but useful.
- Page has author/contact/editorial cues.
- No fake reviews, fake expert, fake stats.
- Allow useful crawlers unless site policy blocks them intentionally.

## llms.txt optional
For documentation/knowledge hubs, consider `/llms.txt` with:
- Site summary.
- Important hub URLs.
- Canonical docs/articles.
- Usage notes.
This is not official Google ranking factor; use as AI crawler convenience only.

## Pitfalls
- Publishing long AI text without expert review.
- Creating schema for hidden content.
- Using FAQ schema for spammy Q&A.
- Overusing exact keywords.
- No sources on legal/finance/policy content.
- “AI SEO” hype with no user value.

## Verification
- Rich Results Test pass.
- Schema Markup Validator pass.
- Rendered HTML contains answer blocks and schema.
- GSC index and enhancement reports clean.
- Search snippets/AI answers cite or align with page over time.
- User can understand page without reading every word.