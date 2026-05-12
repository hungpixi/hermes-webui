# Session note: audit honesty, CSS overflow, 9Router image endpoint

## User corrections captured

- User challenged a suspicious `100/100` report. Lesson: score alone is not enough; pass requires no issues and stricter visible-content checks.
- User complained that article CSS was scattered and generated images overflowed. Lesson: article image insertion must include shared CSS or template-level figure styles, not only `<figure><img>` HTML.
- User noticed perceived short/thin pages despite metadata showing 20+ minutes. Lesson: recount visible words and inspect layout/readability; do not trust readingTime metadata alone.

## Practical checks to run after article/image work

```bash
python - <<'PY'
import re,json,html
from pathlib import Path
arts=json.load(open('content/articles.json',encoding='utf-8'))
issues=[]
for a in arts:
    if a.get('status')!='published':
        continue
    p=Path('content/articles')/(a['slug']+'.html')
    s=p.read_text(encoding='utf-8')
    text=re.sub(r'<script[\s\S]*?</script>|<style[\s\S]*?</style>|<[^>]+>',' ',s)
    words=len(re.findall(r'\w+',html.unescape(text)))
    if words < 6500:
        issues.append(('thin-feel', words, a['slug']))
    if len(re.findall(r'<img\b',s,re.I)) < 3:
        issues.append(('images<3', a['slug']))
    if '/assets/article.css' not in s and '.article-visual' not in s:
        issues.append(('missing-article-visual-css', a['slug']))
print('custom_issues', issues)
PY
```

## CSS guardrail for generated article images

Use shared CSS or equivalent:

```css
html,body{max-width:100%;overflow-x:hidden}
img,svg,video,canvas{max-width:100%;height:auto}
.article-visual,.hero-figure{width:100%;max-width:1040px;margin:28px auto;border:1px solid rgba(30,41,59,.16);border-radius:22px;overflow:hidden;background:#fff}
.article-visual img,.hero-figure img{display:block;width:100%;height:auto;max-height:640px;object-fit:contain;background:#f8fafc}
.article-visual figcaption,.hero-figure figcaption{padding:12px 16px;border-top:1px solid rgba(30,41,59,.16);color:#475569;font-size:14px;line-height:1.5}
table{max-width:100%;display:block;overflow-x:auto}
```

## 9Router/Codex image endpoint lesson

For Codex image models such as `cx/gpt-5.4-image`, prefer binary endpoint per 9Router image skill:

```txt
POST /v1/images/generations?response_format=binary
```

Do not assume `response_format=b64_json` works for every provider/model. If generation hangs or returns no useful body, inspect model info and use provider-specific output format.

## Reporting rule

When user questions quality, answer with exact findings first: word counts, missing CSS/image counts, validation results. Avoid broad claims like “done” until custom checks, JSON parse, server syntax, and SEO audit all pass.
