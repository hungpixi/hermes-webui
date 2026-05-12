# Session note: live-site verification and hero layout correction

## Trigger
User clarified that checks must be against the official live site, not localhost, then corrected homepage hero layout: the article/process image and text (`Quy trình quyết định...`, `Bắt đầu tại bài trụ cột`) were in the wrong hero position.

## Durable lessons
- Verify `https://doanhnghiep1nguoi.io.vn` directly when user asks about website quality. Do not rely on localhost/build-only checks.
- If Python `urllib` receives 403 from Cloudflare, retry with browser-like `User-Agent` or use `curl -A 'Mozilla/5.0'`. Do not label site down from first non-browser request.
- Live article quality check should fetch `/articles.json`, then each `canonicalPath`, and verify:
  - visible words >= 2000
  - `<img>` count >= 3
  - `/assets/article.css`
  - `application/ld+json`
  - canonical/meta description
  - exactly one H1
- Homepage hero for this site should keep primary copy visible in one strong column when requested. Avoid placing article image/card in hero if it creates wrong emphasis; keep quick navigation text compact and move article images to lower card grids.

## Quick live probe
```bash
python - <<'PY'
import urllib.request,json,re,html
base='https://doanhnghiep1nguoi.io.vn'
opener=urllib.request.build_opener(); opener.addheaders=[('User-Agent','Mozilla/5.0')]
arts=json.loads(opener.open(base+'/articles.json',timeout=30).read().decode())
issues=[]; mins=[]; imgmins=[]
for a in arts:
    data=opener.open(base+a['canonicalPath'],timeout=30).read().decode('utf-8','ignore')
    text=re.sub(r'<(script|style)[\s\S]*?</\1>',' ',data,flags=re.I)
    text=html.unescape(re.sub(r'<[^>]+>',' ',text))
    words=len(re.findall(r'\w+',text,re.U)); imgs=len(re.findall(r'<img\b',data,re.I))
    mins.append(words); imgmins.append(imgs)
    checks=[words>=2000, imgs>=3, '/assets/article.css' in data, 'application/ld+json' in data, 'rel="canonical"' in data, '<meta name="description"' in data, len(re.findall(r'<h1\b',data,re.I))==1]
    if not all(checks): issues.append((a['slug'], words, imgs, checks))
print('live_articles',len(arts),'live_min_words',min(mins),'live_min_images',min(imgmins),'live_issues',issues)
PY
```
