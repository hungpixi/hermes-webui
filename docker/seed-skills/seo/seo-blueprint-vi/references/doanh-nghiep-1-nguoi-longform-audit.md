# Doanh Nghiệp 1 Người long-form article audit lesson

Session lesson: user rejected `7 phút` / `8 phút` labels as fake/thin for main articles. Future work on this repo should treat main published content as long-form by default.

## Audit pattern
Run a script or equivalent check for every `content/articles/*.html` plus `content/articles.json`:
- actual visible word count after stripping script/style/HTML
- `readingTime` metadata number
- exactly one H1
- H2 labels that repeat generic template scaffolds
- whether visible content contains a meaningful update, not only date changes

Generic public H2s to avoid or rename:
- `Tóm tắt tốt nhất`
- `Áp dụng cho ai`
- `Điểm quan trọng nhất`
- `Bối cảnh thực tế`
- `Bảng tự kiểm tra`
- `Quy trình 5 bước`
- `Sai lầm thường gặp`
- `Checklist hành động`
- `FAQ`

## Fix pattern
For short/thin legal-tax/support posts, rewrite HTML body, title/meta, and `articles.json` together:
1. Human intro: real Vietnamese situation/tension.
2. Fast answer and stance sentence.
3. Quick risk classification table.
4. Article-specific H2s keyed to reader decisions.
5. One concrete case story: timeline, channel, amount/range, turning point, next action.
6. Checklist around `20 phút` or `30 phút` with data to gather.
7. Source note near legal/tax claims and source-checked date.
8. Internal links to related cluster articles.

Never fix by metadata alone. If `readingTime` says `20 phút`, visible content must have long-form depth and concrete value units.

## Validation snippet
```bash
python - <<'PY'
import os,re,json,html
arts=json.load(open('content/articles.json',encoding='utf-8'))
meta={a['slug']:a for a in arts}
badset={'Tóm tắt tốt nhất','Áp dụng cho ai','Điểm quan trọng nhất','Bối cảnh thực tế','Bảng tự kiểm tra','Quy trình 5 bước','Sai lầm thường gặp','Checklist hành động','FAQ'}
viol=[]
for fn in sorted(os.listdir('content/articles')):
    if not fn.endswith('.html'): continue
    s=open('content/articles/'+fn,encoding='utf-8').read(); slug=fn[:-5]
    text=re.sub('<script[\\s\\S]*?</script>',' ',s,flags=re.I)
    text=re.sub('<style[\\s\\S]*?</style>',' ',text,flags=re.I)
    text=html.unescape(re.sub('<[^>]+>',' ',text))
    words=len(re.findall(r'\\w+',text,re.U))
    h1=re.findall(r'<h1[^>]*>',s,re.I)
    h2=[html.unescape(re.sub('<[^>]+>','',m)).strip() for m in re.findall(r'<h2[^>]*>([\\s\\S]*?)</h2>',s,re.I)]
    bad=[x for x in h2 if x in badset]
    rt=meta.get(slug,{}).get('readingTime','')
    n=int(re.search(r'\\d+',rt).group()) if re.search(r'\\d+',rt) else 0
    if len(h1)!=1 or bad or n<20:
        viol.append((slug,words,rt,len(h1),bad))
print('VIOLATIONS',viol)
PY
```
