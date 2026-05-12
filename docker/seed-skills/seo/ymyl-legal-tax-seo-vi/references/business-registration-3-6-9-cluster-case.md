# Business registration 3-6-9 cluster case

Use when upgrading a Vietnamese YMYL business-registration article from a single strong post into a topic cluster/hub.

## Goal
Raise topic authority from ~90 to 93–95 by adding support posts and fixing homepage IA, not by making one article longer.

## Pattern
1. Keep main pillar skimmable (target visible reading time 7–8 minutes if user says readers skim).
2. Add 3 support posts for long-tail intent:
   - `freelancer-co-can-dang-ky-kinh-doanh-khong`
   - `ban-template-ebook-khoa-hoc-online-co-can-dang-ky-kinh-doanh-khong`
   - `khach-cong-ty-yeu-cau-hoa-don-ca-nhan-lam-sao`
3. Each support post must include:
   - answer-first lead
   - YMYL disclaimer
   - self-check table
   - signs/checklist sections
   - FAQPage schema
   - Article/BreadcrumbList/Organization/Person/WebSite/ItemList schema if project validator requires it
   - official source links
   - internal links back to pillar, tax/invoice article, and relevant operations page
4. Update `content/articles.json` and source HTML together.
5. Update homepage, not only article index.

## Homepage 3-6-9 IA
For a 3-column grid homepage, avoid incomplete rows. Use:
- 3 pillar/orientation cards
- 6 legal/tax/risk-reduction cards
- 9 featured route cards

Example sections:
- `3 trụ cột nên đọc trước`
- `6 bài pháp lý/thuế để tự kiểm tra`
- `9 bài nổi bật theo lộ trình`

Update stats/copy (`7+` → actual article count) and remove old groups with only 2 cards that make columns look unfinished.

## Validation
Run:
```bash
npm run build
```
Expected validator requirements in this project:
- >= 1800 words per article
- legacy labels somewhere in page text: `Tóm tắt tốt nhất`, `Điểm quan trọng nhất`, `Áp dụng cho ai`, `Việc cần làm ngay`, `Rủi ro nếu hiểu sai`, `Nguồn & ngày kiểm tra`
- traffic CTA text: `Đọc tiếp để hiểu đúng hơn` or `Đọc bài liên quan`
- schema types include `Article`, `FAQPage`, `BreadcrumbList`, `Organization`, `WebSite`, `ItemList`

## Live verification pitfall
Browser/live page may show old hero stats after push due deploy/CDN cache. Distinguish:
- source + GitHub correct
- build validation passed
- live cache/deploy still catching up

If live remains stale, trigger deploy or purge cache rather than rewriting source repeatedly.
