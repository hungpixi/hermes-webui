# Vietnamese static SEO hub cleanup pattern

Session pattern from `doanhnghiep1nguoi` static site.

## User requirements captured

- Site articles had poor quality because visible top headings used no-accent Vietnamese.
- CTA must point to `hungphamphunguyen@gmail.com`.
- Public SEO Machine link should point to `https://phamphunguyenhung.com`.
- Footer should mimic business-style trust footer with 3 layers/columns and placeholders for DMCA and Bộ Công Thương icon/link.

## Files touched in that pattern

- `index.html` — homepage CTA, SEO Machine link, footer 3-column trust layer.
- `scripts/generate-cms.mjs` — source of truth for article labels, CTA email, JSON-LD, generated footer.
- `scripts/validate-content.mjs` — validation expectations for new email and accented top blocks.
- `content/site.config.json` — brand contact email and minimum article block names.
- `content/articles.json` — per-article CTA href/label.
- `content/articles/*.html` — rendered articles if regeneration is not run.
- `README.md` — documented required top blocks.

## Concrete changes worth reusing

Visible top blocks should be accented:

- `Tóm tắt tốt nhất`
- `Điểm quan trọng nhất`
- `Áp dụng cho ai`
- `Việc cần làm ngay`
- `Rủi ro nếu hiểu sai`
- `Nguồn & ngày kiểm tra`

Schema improvement in generator:

```js
contactPoint: [{
  '@type': 'ContactPoint',
  email: contactEmail,
  contactType: 'customer support',
  availableLanguage: ['vi', 'en']
}],
sameAs: [seoMachineUrl]
```

Footer pattern:

1. Brand + description + contact email.
2. Legal/trust: DMCA Protected + Bộ Công Thương placeholder with note that official link/icon will be added later.
3. Ecosystem: homepage, content index, SEO Machine domain.

## Verification search

After edits, search should return zero for stale strings:

```regex
Tom tat tot nhat|Diem quan trong nhat|Ap dung cho ai|Viec can lam ngay|Rui ro neu hieu sai|Nguon &amp; ngay kiem tra|hello@doanhnghiep1nguoi\.io\.vn|seomachine\.phamphunguyenhung\.com
```

## Caution

Do not replace infrastructure domains such as `9router.phamphunguyenhung.com` when user only asked public SEO/product link to move. Keep API/router config unless explicitly requested.
