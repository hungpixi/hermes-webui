# Static file 404 after Dokploy deploy: Dockerfile COPY allowlist

Use when Dokploy deploy succeeds but a newly added static file URL returns app-level `404 Not found` while older routes still work.

## Symptom

- Git commit/push OK.
- `POST /api/application.deploy` returns `200`.
- Homepage or existing files serve normally.
- New file path, e.g. `/seo-machine.html`, returns `404 Not found` from app.
- Files under already-copied directories (e.g. `/reports/...`) may work.

## Root cause found

Dockerfile used fixed root-file allowlist:

```Dockerfile
COPY package.json server.js index.html sitemap.xml robots.txt llms.txt ./
COPY content ./content
COPY reports ./reports
```

New root file `seo-machine.html` existed in repo but was not copied into image.

## Fix pattern

Add new file to Dockerfile allowlist:

```Dockerfile
COPY package.json server.js index.html seo-machine.html sitemap.xml robots.txt llms.txt ./
```

Then:

```bash
npm run build
git add Dockerfile
git commit -m "Include new static page in Docker image"
git push
# trigger Dokploy deploy
```

## Verification pattern

After deploy, poll both new root file and copied-directory files. First poll can hit old container during rollout; retry for 30-90 seconds.

```bash
python - <<'PY'
import urllib.request, urllib.error, time
urls=[
  'https://example.com/new-page.html',
  'https://example.com/reports/demo.html',
]
for i in range(6):
    print('TRY', i+1)
    for url in urls:
        try:
            req=urllib.request.Request(url,headers={'User-Agent':'Mozilla/5.0','Cache-Control':'no-cache'})
            with urllib.request.urlopen(req,timeout=20) as r:
                body=r.read(200000).decode(errors='ignore')
                print(url, r.status, len(body))
        except urllib.error.HTTPError as e:
            print(url, 'HTTPERR', e.code, e.read(120).decode(errors='ignore'))
    time.sleep(10)
PY
```
