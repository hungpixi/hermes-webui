# Session note: homepage decision map + CMS deploy workflow (2026-05)

Use for future `doanhnghiep1nguoi` homepage/CMS/deploy tasks.

## What user pushed back on
- Homepage looked not strong enough after earlier improvement.
- User wanted upgrade, commit, and Dokploy deploy.
- Avoid claiming deploy if no Dokploy CLI/API/token is available.

## Homepage pattern that worked
Turn homepage from generic content hub into decision map:
1. Sharper H1: outcome/transition, not dictionary title.
2. Quick map section: `Bắt đầu bằng câu hỏi đúng`.
3. 10-minute quick-start ordered list.
4. Situation quiz with direct links:
   - khách đều / đăng ký kinh doanh
   - khách công ty hỏi hóa đơn
   - dựng nền móng
   - viết bài mới in CMS
5. Proof bar: articles, images, CMS, CI.
6. Article library as product cards: image + tag + reason to read + reading time.
7. Operator/sidebar: role-based quick read path.
8. CMS strip CTA linking `/admin/`.
9. Remove stray inline styles while editing.
10. Validate one H1 and JSON-LD parse separately from article SEO audit.

## Validation sequence before commit
```bash
node scripts/build-articles.mjs
python -m json.tool content/articles.json > /dev/null
node --check server.js
python scripts/seo_95_audit.py --min-score 95
python - <<'PY'
from pathlib import Path
import re,json
s=Path('index.html').read_text(encoding='utf-8')
assert len(re.findall(r'<h1\\b',s,re.I))==1
assert all(x in s for x in ['ban-do-nhanh','product-layout','cms-flow','/admin/'])
for m in re.finditer(r'<script type="application/ld\\+json">([\\s\\S]*?)</script>',s): json.loads(m.group(1))
print('homepage validation ok')
PY
```

## Commit/deploy lesson
- Commit after validation.
- Push `main` to GitHub.
- Watch GitHub Actions SEO audit with `gh run watch <id> --exit-status`.
- If `dokploy` CLI missing and no `DOKPLOY_*` env vars exist, do not say manual deploy was done. Say: pushed to GitHub; Dokploy should auto-deploy if configured; manual Dokploy trigger unavailable from current environment.

## Concrete files involved
- `index.html`
- `admin/index.html`
- `admin/config.yml`
- `content/posts/*.md`
- `scripts/build-articles.mjs`
- `scripts/seo_95_audit.py`
- `.github/workflows/seo-95-audit.yml`
- `assets/article.css`
