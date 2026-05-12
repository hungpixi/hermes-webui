# Nixpacks Next.js runtime 500 after successful Dokploy deploy

Use when Dokploy deploy status is `done` but the custom domain returns `Internal Server Error`.

## Observed pattern
- App cloned and built successfully with Nixpacks.
- Deployment status from `deployment.allByType` is `done`.
- Traefik config maps domain to app service and expected port, e.g. `http://<app>:3000`.
- DNS now resolves and request reaches app, but `/` or `/login` returns HTTP 500.

## Likely cause
Runtime env missing, especially code that executes on every request:
- Next `src/proxy.ts` / middleware
- auth/session refresh helpers
- SDK clients constructed at module/request time with non-null assertions

Example Supabase failure shape:
```ts
const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL;
const supabaseKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY;
createServerClient(supabaseUrl!, supabaseKey!, ...)
```
If Dokploy env lacks these, build can pass but runtime requests crash.

## Triage steps
1. Check public paths:
```bash
python - <<'PY'
import urllib.request
for url in ['https://domain/','https://domain/login','https://domain/api/market-data']:
  try:
    r=urllib.request.urlopen(urllib.request.Request(url,headers={'User-Agent':'Mozilla/5.0'}),timeout=20)
    print(url, r.status, r.getheader('content-type'))
  except Exception as e:
    print(url, type(e).__name__, str(e)[:250])
PY
```
2. Check app env keys only via Dokploy API; never print values.
3. Search runtime/middleware code for `process.env.*!`, `createServerClient`, `createBrowserClient`, SDK constructors, and `throw new Error('Missing ...')`.
4. If all pages fail, inspect Next `proxy.ts` or middleware before page components.

## Fix patterns
- For public/demo deploys: skip optional auth middleware when env missing.
```ts
if (!supabaseUrl || !supabaseKey) {
  return NextResponse.next({ request: { headers: request.headers } });
}
```
- For real authenticated production: set required runtime env in Dokploy app env, not only build secrets.
- Build secrets only affect build; request-time server code needs runtime env.

## Verification
- `npm run build` locally after code/env guard.
- Commit + push private repo.
- Trigger `application.deploy`.
- Poll `deployment.allByType` until `done`.
- Verify `/` and `/login` return `200 text/html`.
