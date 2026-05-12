# Business registration 7-minute skim case

Use when upgrading Vietnamese legal/tax/YMYL articles where user says the article is too long and readers only skim.

## Context

Live article:
`https://doanhnghiep1nguoi.io.vn/bai-viet/khi-nao-can-dang-ky-kinh-doanh`

User feedback: reading time around 24–28 minutes was not acceptable; real readers skim and should finish in about 7 minutes max.

## Successful pattern

1. Keep YMYL safety, but move from pillar-long-form to skim-first decision aid.
2. Update title/kicker/metadata to reflect short read:
   - title pattern: `Khi nào phải đăng ký kinh doanh? Checklist 7 phút cho freelancer và người bán online`
   - kicker pattern: `Pháp lý · 7 phút · cập nhật DD/MM/YYYY`
   - `articles.json.readingTime`: `7 phút`
3. Put the key answer above the fold:
   - short reader-context lead
   - `Câu trả lời ngắn`
   - 3 decision levels: thường chưa cần vội / nên kiểm tra / hỏi chuyên gia trước khi nhận tiếp
   - disclaimer/caveat
4. Main visible path should be scan-friendly:
   - one compact situation table
   - six signal cards/grid
   - hộ kinh doanh vs công ty in 2 short paragraphs
   - five misunderstandings
   - 30-minute checklist
   - FAQ
   - source list
5. Preserve trust and compliance:
   - official sources near claims
   - no invented article numbers
   - legal/tax caveat
   - author/update box
   - internal reading path
6. Preserve validator compatibility without hurting skimmability:
   - if `site.config.json` requires `wordCount: 1800`, keep a lower `Phần đọc kỹ` appendix
   - include required legacy labels there if needed: `Tóm tắt tốt nhất`, `Điểm quan trọng nhất`, `Áp dụng cho ai`, `Việc cần làm ngay`, `Rủi ro nếu hiểu sai`, `Nguồn & ngày kiểm tra`
   - do not expose a top-heavy legacy H2 stack before useful content
7. Keep schema and metadata aligned:
   - JSON-LD graph should include project-required types, e.g. `Article`, `FAQPage`, `BreadcrumbList`, `Organization`, `Person`, `WebSite`, `ItemList`
   - update `content/articles.json.schemaTypes` to match

## Validation used

```bash
npm run build
```

Expected output:

```txt
content ok: 10 articles
```

## Quality score direction

Before: high legal/SEO quality but poor skimming.
After: better user fit because reading path is shorter, with deeper material optional.

Suggested scoring dimensions:
- SEO/YMYL
- read-skimmability
- UI/UX article flow
- legal safety
- metadata/schema consistency
