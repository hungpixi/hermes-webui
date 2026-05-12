---
name: seo-blueprint-vi
description: "End-to-end Vietnamese SEO blueprint for planning, publishing, internal linking, refreshing, and measuring organic growth."
---

# SEO Blueprint VI

Use when turning SEO into an operating system: content briefs, production, internal linking, refresh, and reporting.

## Workflow
1. Define business/site goal and non-goals.
2. Build keyword/entity map.
3. Split into hubs, pillars, support articles, glossary/resources.
4. Create content briefs before drafting.
5. Draft people-first content: reader tension → answer → framework → examples/case → action.
6. Optimize on-page and schema after editorial flow works; never let SEO checklist make the page robotic.
7. Engineer internal links.
8. Publish, update sitemap, request indexing.
9. Monitor GSC weekly.
10. Refresh content every 30–90 days based on data.

## Top-tier article standard
A strong page must feel like a trusted resource, not a checklist dump.

### 5-block opening rule
For pillar/resource/decision-risk posts, open with exactly 5 visible blocks before main body:
1. Human scene: recognizable reader situation, tension, stakes.
2. Fast answer: short, clear, extractable.
3. Quick classification/table: low-risk / check-needed / expert-required or equivalent.
4. TOC: only after reader already understands why page matters.
5. Main body: start real explanation, not more summary modules.

Do not stack these as separate top H2s: `Tóm tắt`, `Điểm quan trọng`, `Áp dụng cho ai`, `Việc cần làm ngay`, `Rủi ro`, `Nguồn`. Merge into compact box, table, note, or move lower/end.

### Human + authority rules
- Opening: show reader's real situation before giving answer.
- Stance: include one clear sentence that reframes issue (`Vấn đề không phải X; vấn đề là Y`).
- Classification: separate low-risk / needs checking / expert-required cases.
- Depth: add one mini case story with timeline, channel, money/range, turning point, next action.
- Section depth: important H2s need more than 1–2 generic paragraphs. Use: `Dấu hiệu là gì?` → `Ví dụ thực tế` → `Rủi ro nếu bỏ qua` → `Việc nên làm` → `Nguồn/điểm kiểm tra` when relevant.
- Flow: avoid stacking generic H2s before core content. Prefer H1 → human intro → fast answer → quick classification table → TOC → main sections.
- Trust: sources, author, caveat, reviewer only if real. If reviewer missing, use professional editorial note, not awkward placeholder.
- Voice: specific, grounded Vietnamese. Avoid “bài SEO máy móc” phrases and repeated “nên kiểm tra” endings.

### 82→88 upgrade pattern
When a page scores “SEO good but still not đã”: remove top module stack, add real opening, rename robotic headings into action headings, add one case story after main table, deepen thin sections with examples/risks/actions, and rewrite awkward trust notes.

### Content length standard
Length must match intent. Do not chase word count, but do not ship thin pages.

Use these default targets for Vietnamese articles on Doanh Nghiệp 1 Người:
- Visible reading time target: generally `20 phút` or longer for main published articles unless user explicitly requests a glossary/micro page. Never fake this by changing metadata only; the HTML must have matching depth and useful value units.
- Main published article minimum: 2,000+ words; many legal/tax/resource articles should land around 3,000–4,800 words. Do not publish short 900–1,400 word posts unless user explicitly asks for glossary/micro page.
- Support article / long-tail question: 2,000–2,800 words minimum, and often 20+ minutes on this site when it carries legal/tax/business risk. Answer fast, add 1 table/checklist, 1 mini example, FAQ, internal next reads.
- Practical guide / checklist: 2,400–3,500 words. Needs steps, examples, mistakes, tools/templates, FAQ.
- Pillar/resource/YMYL guide: 3,000–4,800 words. Needs human opening, classification, main table, case story, deep sections, checklist, FAQ, sources, internal cluster.
- Ultimate guide / hub page: 4,500–7,000 words only if it has strong structure and real subtopics. Otherwise split into cluster pages.

HTML/page structure must follow the gold standard, not ad-hoc layout:
1. `<div class="kicker">` with category, reading time, update date.
2. Single `<h1>`.
3. Human intro paragraphs before first SEO box.
4. Hero figure if article has image.
5. Compact fast-answer note/box.
6. Quick classification table.
7. Caveat/source note if YMYL.
8. `<nav class="toc">` after answer/classification.
9. Main reader-oriented `<h2>` sections, starting with main action table.
10. Case story after main table for pillar/resource/YMYL.
11. Deep sections with examples/risks/actions.
12. Checklist/FAQ/trust/source/internal-link cluster near end.
13. JSON-LD schema matching visible content.

Do not expose legacy validator headings as public top H2 stack. If compatibility labels are needed, keep them non-disruptive or update validator.

Minimum quality density:
- Every 700–900 words should add at least one concrete value unit: table, checklist, example, source synthesis, case, decision rule, template, or comparison.
- No H2 should be only 1 generic paragraph in pillar/YMYL pages.
- If article passes target length but feels repetitive, cut 20–40% and add examples instead.
- If article is below target, do not pad. Add missing reader decisions, real examples, source notes, and next actions.
- If user complains that `7 phút`/`8 phút` articles are “ảo”, audit every article for metadata reading time, actual word count, H1 count, generic repeated H2s, and visible update value. Fix both HTML and `content/articles.json`; do not only update `readingTime`.

## Content brief template
```md
URL:
Primary keyword:
Search intent:
Reader problem:
Reader anxiety / lived situation:
SERP pattern:
Unique angle:
Core stance sentence:
Risk / decision classification:
Required entities:
Required sources:
Required case story or example:
Internal links in:
Internal links out:
Schema type:
Outline:
FAQ:
Done criteria:
```

## Internal link rules
- Each support article links to parent pillar.
- Pillar links to best support articles.
- Related articles link sideways when intent overlaps.
- Use descriptive anchor text, not “click here”.
- Avoid repeating exact-match anchor mechanically.
- Related links must be real next reads.

## Refresh workflow
1. Pull GSC pages with declining clicks or high impressions/low CTR.
2. Compare current SERP.
3. Add missing sections, examples, FAQ, sources.
4. Remove outdated claims.
5. Update title/meta only when CTR issue exists.
6. Update `dateModified` only after meaningful change.
7. Request indexing.
8. Recheck after 2–6 weeks.

## Checks
- No cannibalization: one intent, one URL.
- URL stable.
- Title/meta unique.
- H1 unique.
- CTA/next step matches site goal.
- Sitemap updated.
- Schema valid.
- No broken internal/external links.

## Starter + Plugin Audit Merge
Use this section instead of separate generic starter/plugin skills when doing quick SEO hygiene, first-traffic setup, or Yoast/RankMath-style audit.

### First-traffic roadmap
1. Confirm crawlability: robots, sitemap, canonical, status codes, no accidental `noindex`.
2. Verify Search Console and submit sitemap.
3. Map one primary intent per URL.
4. Fix title, meta description, H1, intro answer, internal links, and schema.
5. Publish cluster in order: hub/pillar first, then support articles, then glossary/resources.
6. Request indexing for meaningful new/updated pages.
7. Review GSC after 2–6 weeks; improve pages with impressions but weak CTR/clicks.

### Google starter guide principles
- Make pages helpful for people before search engines.
- Use descriptive titles, headings, URLs, alt text, and internal links.
- Avoid duplicate/thin pages and doorway-style keyword variants.
- Keep site crawlable, fast enough, mobile-friendly, and secure.
- Use structured data only when visible content supports it.

### Yoast/RankMath-style audit
Check without letting plugin score distort article voice:
- Primary keyphrase appears naturally in title, H1 or early intro, and a few relevant sections.
- Meta title/description match intent and encourage clicks.
- Headings form clean outline; no robotic checklist stack.
- Paragraphs readable; complex legal/tax points use examples/tables.
- Internal links point to real next reads with descriptive anchors.
- Images have useful alt text when image matters.
- Schema matches visible content.
- FAQ answers are concise, non-duplicative, and useful.
- No artificial keyword density work.

## Pitfalls
- Publishing many posts without cluster plan.
- Refreshing dates without content changes.
- Exact-match anchor spam.
- Over-optimizing for plugin score while hurting reader.
- Forgetting Search Console feedback.