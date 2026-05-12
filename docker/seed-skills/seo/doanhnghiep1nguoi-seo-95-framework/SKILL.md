---
name: doanhnghiep1nguoi-seo-95-framework
description: "95-point SEO/content framework for Doanh Nghiệp 1 Người: storytelling, YMYL trust, long-tail FAQ, schema, internal clusters, and CI audit."
---

# Doanh Nghiệp 1 Người SEO 95 Framework

Use when auditing, writing, refreshing, or automating content for `d:/code/doanhnghiep1nguoi`.

## Audience and intent
Primary readers:
- Cá nhân bắt đầu bán hàng/dịch vụ online.
- Freelancer, solo founder, creator, người bán sản phẩm số: template, ebook, khóa học.
- Người nhận chuyển khoản cá nhân, chưa có mô hình doanh nghiệp hoàn chỉnh.

Content goals:
- Help readers self-classify: giao dịch cá nhân lẻ vs mô hình kinh doanh.
- Give practical next actions: checklist, classification tables, case story.
- Explain law, tax, invoice, legal responsibility in readable, grounded Vietnamese.

Keyword strategy:
- Primary: đăng ký kinh doanh, hộ kinh doanh, solo founder, freelancer, bán hàng online.
- Long-tail: freelancer có cần đăng ký kinh doanh không, bán template online có cần đăng ký, nhận tiền cá nhân có sao không, khách công ty yêu cầu hóa đơn, doanh nghiệp 1 người là gì.

Tone:
- Friendly, storytelling, practical, accurate, source-backed.
- Avoid dry legal prose and AI-like module stacks.

## UX-centric 95-point principles
- Open with the reader's real situation. Use direct `bạn` language: Facebook/Zalo jobs, personal transfers, clients asking for contract/invoice, first repeat customers.
- Fast answer must be memorable and action-oriented: `thường chưa cần`, `nên kiểm tra`, `cần hỏi chuyên gia ngay`, `việc làm trong 30 phút tới`.
- Classification tables must be visual/scannable: situation -> risk level -> why it matters -> action -> official source. Use icons/labels like 🟢 thấp, 🟡 cần kiểm tra, 🔴 xử lý trước.
- Checklist must feel actionable and tickable: checkbox list, 20 recent transactions, mark customer type, save evidence, write accountant/lawyer questions.
- Case story must show progression: ban đầu -> vấn đề -> giải pháp -> lesson learned. Include Vietnam-specific details: Facebook/Zalo, bank transfer, company client, invoice, contract, nghiệm thu.
- Each deep section should follow: description -> example -> action -> source check.
- Use risk/tip boxes with visual cues: ⚠️ risk, ✅ tip, 📌 next action.
- FAQ should be short, direct, long-tail, and match actual search language.
- Reading flow: story -> problem -> action -> lesson -> FAQ -> CTA. Paragraphs should usually be 3-5 sentences max.

## Audit honesty rule
- Do not report `100/100` or `95+` from a score alone if the audit output still lists issues. A pass requires both score threshold and empty issue list.
- If images are pending, report content/schema score separately from image completion.
- When the user corrects a false/overstated report, stop summarizing success, name the exact audit weakness, tighten the validator, and continue work.
- When a high score looks suspicious, rerun with a stricter validator that checks real visible content: word count, issue list, figure/image count, FAQ count, case specificity, source proximity, schema types, and CSS/overflow risks. Report the stricter score, not the convenient score.
- Do not use generated filler blocks to game the audit. If adding sections, make them article-specific and check that they are readable, not just keyword-bearing.

## 95-point article template
Each main article should include 7–10 strong sections:

1. H1 + storytelling opening
   - Put reader into real situation.
   - Pain point: bắt đầu nhỏ, rồi khách hỏi hóa đơn/hợp đồng/thuế.
   - Reader should feel: “mình trong câu chuyện này”.

2. Fast answer / key takeaway
   - 3–4 direct, actionable bullets.
   - Split: thường chưa cần / nên kiểm tra / cần hỏi chuyên gia ngay.
   - Good for featured snippets.

3. Quick classification table
   - At least 8–10 situations.
   - Columns: tình huống, mức độ cần kiểm tra, vì sao quan trọng, việc nên làm, nguồn tham khảo chính thống.

4. Six warning signals
   - Each signal has: mô tả, ví dụ thực tế, việc cần làm, nguồn/link chính thống.
   - Common signals: bán thường xuyên, khách yêu cầu hợp đồng/hóa đơn, giá trị hợp đồng tăng, ngành nghề có điều kiện, mở rộng kênh website/affiliate/ads, cần tách tiền cá nhân và tiền kinh doanh.

5. Real case story
   - At least one case per pillar/YMYL/resource article.
   - Show timeline: 0 → có khách đều → hóa đơn/hợp đồng/decision point.

6. Deep solution section
   - Examples: hộ kinh doanh hay công ty, checklist 30 phút trước khi hỏi kế toán/luật sư, AI workflow cho solo founder.
   - Must be action-oriented.

7. Five common mistakes
   - Each mistake includes consequence + how to avoid.

8. Long-tail FAQ
   - At least 12 questions for pillar/YMYL/resource articles.
   - Direct short answers.
   - FAQPage schema must match visible FAQ.

9. Author/reviewer/trust box
   - Author name + role + project.
   - Reviewer only if real accountant/lawyer reviewed.
   - Update date, source checked date, disclaimer.

10. Sources and internal links
   - Official sources near claims, not only bottom.
   - Descriptive internal anchors to pillar/cluster pages.

## Site SEO/UX checklist
- Title/meta unique. Meta title <70 chars if possible; meta description <160 chars if possible.
- One H1. H1 contains primary/long-tail naturally.
- H1 → H2 → H3 hierarchy, no repeated generic H2s.
- TOC for article >1500 words.
- Paragraphs short for mobile; use bullets, tables, callout boxes.
- At least one case story per article unless narrow micro page.
- E-E-A-T: author, reviewer if real, sources, update date, disclaimer.
- Schema: Article + FAQPage + BreadcrumbList + Person/Organization when visible/real.
- Internal cluster: pillar ↔ support links.
- Page speed: image size reasonable; no unnecessary heavy assets.
- Shared article CSS must style generated figures/images (`.article-visual`, `.hero-figure`) so 1280px images cannot overflow. Verify `img{max-width:100%;height:auto}`, figure max-width/margins, table horizontal scroll, and mobile breakpoints.
- Avoid leaving CSS "loạn xạ" across article files when doing repeated edits. Prefer shared `assets/article.css` or template-level CSS, then only minimal per-page CSS if unavoidable.
- Do not accept a page as complete because metadata says 20+ minutes. Recount visible words and inspect perceived thinness/readability; minified one-line HTML or dense autogenerated blocks can still feel bad to users.

## Pillar and cluster strategy
Pillar:
- `khi-nao-can-dang-ky-kinh-doanh`

Core cluster:
- `doanh-nghiep-1-nguoi-la-gi`
- `thue-hoa-don-ke-toan-cho-nguoi-moi`
- `checklist-7-ngay-dung-nen-mong`
- `10-viec-ai-co-the-lam-de-mua-lai-thoi-gian`

Support long-tail:
- `freelancer-co-can-dang-ky-kinh-doanh-khong`
- `ban-template-ebook-khoa-hoc-online-co-can-dang-ky-kinh-doanh-khong`
- `khach-cong-ty-yeu-cau-hoa-don-ca-nhan-lam-sao`
- `hoa-don-dien-tu-may-tinh-tien-ho-kinh-doanh`

Every cluster article should link back to pillar with descriptive anchor.

## User workflow corrections learned
- If user says "sửa nội dung trước rồi chèn ảnh sau", prioritize content quality, schema, internal links, and audit score before image insertion.
- Reading-time target for current site is 20+ minutes per published article unless user explicitly requests a short skim page.
- Do not mark 7–8 minute YMYL/support posts as complete for this site; expand with real decision tables, cases, sources, FAQ, and internal links.
- Avoid generic public H2 stacks across articles. Reused validator labels should be rewritten into article-specific headings.

## 100-point audit scoring
Score each article across 14 checks:
1. Storytelling opening / pain point.
2. Fast answer with actionable classification.
3. Classification table with 8–10 situations.
4. Warning signals/checklist with example/action/source.
5. Case story.
6. Deep solution section.
7. Five common mistakes.
8. FAQ long-tail >=12.
9. Author/reviewer/trust box.
10. Sources near claims and source checked date.
11. Internal links pillar/cluster.
12. Heading hierarchy and mobile readability.
13. Schema: Article, FAQPage, BreadcrumbList, Person/Organization.
14. Images: >=3 useful article images, alt/caption, JSON-LD image.

Goal: each article >=95/100. CI should fail below 95 unless threshold explicitly changed.

## Automation workflow
- Add a local audit script under `scripts/seo_95_audit.py`.
- Add GitHub Actions workflow to run on push/PR.
- Audit should parse `content/articles.json` and each `content/articles/*.html`.
- Fail CI when any published article scores <95 or has critical errors: <1 H1, <20 min reading time, missing FAQ schema when FAQ visible, missing 3 images, missing canonical, missing sourceCheckedAt.
- Sequence matters for this site: first repair article substance and schema to the 95-point framework, then generate/chèn images. Do not block content repair on pending 9Router image batches.
- When image generation is running in background, continue content/schema audit in parallel if it does not modify generated asset outputs.
- Treat user pushback like “m ảo à sao 100 được” as a hard signal to tighten validators immediately: rerun with strict issue-list checks, report average plus every remaining issue, and do not claim completion from a permissive score.
- For homepage/content hub work, apply UX research patterns before editing: hero with reader pain, situation selector, reading journey, grouped image cards, sticky/quick read path, risk matrix, source/trust notes, and ItemList schema. Validate homepage separately from article audit; do not imply article SEO audit is Lighthouse/homepage UX score.
- For Codex/9Router image generation, prefer the skill-documented binary endpoint `/v1/images/generations?response_format=binary` for `cx/gpt-5.x-image`; avoid `b64_json` if it hangs or fails to stream clearly. Verify missing assets by comparing `content/article-image-plan.json` to `assets/`, then insert `<figure>`/`alt`/captions and JSON-LD images together.
- When packaging this framework as a reusable AI-agent repo, include `SKILL.md`, `scripts/seo_95_audit.py`, workflow template, AGENTS template, image plan example, README, `.gitignore`, and a secret scan before pushing private.
- When the site starts becoming hard to maintain by direct HTML edits, move to a CMS/source-of-truth flow: Sveltia CMS under `/admin`, `content/posts/*.md` frontmatter/body as editable source, `scripts/build-articles.mjs` as the only generator for `content/articles/*.html` and `content/articles.json`, and CI that runs generator before JSON/server/SEO audit. Do not keep manually patching generated HTML as the primary workflow.
- CMS generator must protect quality automatically: require 2000+ visible words (target 2200+ in AI prompt), 20+ minute target for this site, 3 images, related links, official sources, 12+ FAQ, caveat; then inject shared article CSS, `og:image`, schema, fallback classification table, warning signals, case story, mistakes, checklist, source section, internal links, CTA, and author/disclaimer if editor body misses them. Generated fallback sections are guardrails, not a substitute for article-specific writing.
- Daily content workflow must not stay on old thresholds. Keep `content/site.config.json` minimum word count at 2000+, make `validate-content.mjs` fail on `<2000` words, `<3` images, missing image CSS, missing canonical/meta/JSON-LD/OG image, missing internal links/source/CTA, and wire `npm run build` to run `build-articles.mjs`, `validate-content.mjs`, and `scripts/seo_95_audit.py --min-score 95`. Include `content/posts/*.md`, generated articles, and image assets in daily auto-commit file patterns.
- Homepage upgrades should be decision-led, not only prettier. If user says homepage is still not good enough, convert it from a generic content library into a decision map: sharper H1, quick-start section, role/situation quiz, proof bar, product-card article grid with images and reasons to read, sticky/operator sidebar, CMS entry CTA, source/trust area, and valid JSON-LD. Remove stray inline styles while doing this.
- Homepage hero layout preference for this site: keep hero readable in one strong column when user asks for full visibility. Do not let an article image/card (“Quy trình quyết định…”, “Bắt đầu tại bài trụ cột”) dominate or sit in the wrong hero position. Keep quick-start content as compact text/navigation panel; move article images into article cards or lower hub sections. Validate live hero text/position, not only local diff.
- Commit/deploy workflow for this repo: validate first (`node scripts/build-articles.mjs`, JSON parse, `node --check server.js`, SEO audit, homepage JSON-LD/H1 checks), then commit and push `main`. Dokploy may auto-deploy from GitHub; if no Dokploy CLI/API token is present, say exactly that and report GitHub Actions status instead of pretending manual deploy happened.

## Reference notes
- `references/session-2026-05-seo95-and-image-workflow.md` — session-proven sequence for 20+ minute articles, 95-point audit automation, content-first repair, and later 9Router/Codex image insertion.
- `references/session-2026-05-audit-honesty-css-images.md` — corrections about suspicious audit scores, visible word checks, generated image CSS overflow, and 9Router/Codex binary endpoint.
- `references/session-2026-05-cms-generator-flow.md` — Sveltia CMS + Markdown source-of-truth + deterministic generator pattern for future article production.
- `references/session-2026-05-homepage-cms-deploy.md` — decision-map homepage upgrade pattern plus validation/commit/Dokploy honesty workflow.
- `references/session-2026-05-daily-workflow-2000-images.md` — daily content workflow gates for 2000+ words, 3 images, SEO validation, and auto-commit file patterns.
- `references/session-2026-05-live-hero-layout-correction.md` — live-site verification probe and homepage hero layout correction notes.

## Refresh workflow
1. Audit all articles.
2. Sort by lowest score.
3. Fix content substance before metadata.
4. Add missing case, table, FAQ, source notes, images, internal links.
5. Update visible HTML and `content/articles.json` together.
6. Validate JSON/schema/build.
7. Commit only after all articles >=95.
