# Business registration article static-site case

Use as compact example for upgrading a Vietnamese YMYL legal/tax article on a static content site.

## Target transformation
Turn generic legal guide into authoritative resource for:
- cá nhân
- freelancer
- solo founder
- người bán online Việt Nam
- creator / sản phẩm số / template / dịch vụ online

## Required content upgrades
- Title: `Khi nào phải đăng ký kinh doanh? Checklist cho cá nhân, freelancer, solo founder và người bán online`
- Meta: `Bạn bán hàng online, làm freelancer, bán template, dịch vụ số hoặc nhận job qua Zalo/Facebook? Xem khi nào cần kiểm tra đăng ký kinh doanh, hóa đơn, hợp đồng, thuế và dòng tiền.`
- Add 3-layer conclusion immediately after quick answer:
  1. Usually not urgent for a few personal-asset sales, no repetition, no price list, no promotion, no business-like operation.
  2. Check registration if selling regularly, profit intent, repeat customers, price list, website/store/channel, or frequent receipts.
  3. Ask accountant/lawyer fast if company client needs invoice, large contract, conditional business line, sensitive data, or steadily rising revenue.
- Main 10-row table: personal asset sales, weekly online selling, recurring freelancer jobs, templates/ebooks/courses, website/landing/storefront, company invoice request, conditional business line, rising revenue/regular inflow, international receipts, hiring collaborators.
- Add H2 `Nên đăng ký hộ kinh doanh hay công ty?` with 5 criteria: customer type, invoice/contract/acceptance needs, conditional/risky industry, hiring/funding/team expansion, monthly accounting/tax/ops cost vs current revenue.
- Add H2 `5 tình huống dễ hiểu sai` covering Facebook/Zalo misconception, small revenue, invoice later, company for professionalism, solo without system.
- Replace vague checklist with `Checklist 30 phút trước khi hỏi kế toán hoặc luật sư`: record 20 recent transactions, mark 6 signals, write 5 concrete questions, decide only after data.
- FAQ 10–12 long-tail questions.
- Author/reviewer box. Never invent professional reviewer; say not published/only add after real review.
- Internal link cluster by reader route, not generic “read more”.

## Static repo implementation pattern
- Update HTML title/meta/OG/body/schema in article file.
- Update metadata index (`content/articles.json` in the case site) with matching `seoTitle`, `metaDescription`, `excerpt`, `readingTime`, `relatedSlugs`, `entities`, `mentions`, and `schemaTypes`.
- Preserve site shared style/footer unless redesign requested.
- If validator requires legacy section labels, keep those labels while strengthening content.
- Keep actual JSON-LD and metadata `schemaTypes` aligned.
- Run `npm run build` or project validator before commit.
- Commit/push to main and verify live URL after deploy.
