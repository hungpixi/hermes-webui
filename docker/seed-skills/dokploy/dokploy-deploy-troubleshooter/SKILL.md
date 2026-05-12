---
name: dokploy-deploy-troubleshooter
description: Troubleshoot generic Dokploy deploy issues with API-first workflow (app source, domain mapping, redeploy, and verification). Use when app is not specifically 9Router and problem is wrong source, dead domain, 404/502, or port mapping mismatch.
---

# Dokploy Deploy Troubleshooter

Use this skill when deploying/updating apps on Dokploy and seeing common failures like `404 page not found`, wrong repo source, deploy succeeds but domain dead, or app mapped to wrong port.

## Source Of Truth
- Dokploy Core docs:
  - https://docs.dokploy.com/docs/core
  - https://docs.dokploy.com/docs/core/troubleshooting
  - https://docs.dokploy.com/docs/core/domains
  - https://docs.dokploy.com/docs/core/applications
  - https://docs.dokploy.com/docs/core/auto-deploy

## Fast Workflow (API-first)
1. Load Dokploy env (`DOKPLOY_BASE_URL`, `DOKPLOY_API_KEY`) from local secure env file.
2. Find target app id via API route:
   - `GET /api/project.all`
   - Match `project -> environment -> application.name`
3. Verify exact application details with:
   - `GET /api/application.one?applicationId=<id>` when available on that Dokploy version.
   - Some Dokploy installs expose app details only in `GET /api/project.all`, and `/api/application.one` can return `404 NOT_FOUND`; in that case keep using selected fields from `project.all` rather than treating the app as missing.
   - Confirm `sourceType`, owner/repository/branch, buildPath, triggerType/autoDeploy, env port, applicationStatus, domains, and latest deployments when fields are present.
4. Verify source repo/branch:
   - Preferred: `POST /api/application.saveGithubProvider`
   - Fallback: `POST /api/application.saveGitProvider` (custom git)
5. Trigger deploy:
   - `POST /api/application.deploy`
   - Auth header can vary by Dokploy install. Try `Authorization: Bearer <key>` first if that worked before; if API returns `401`, retry with `x-api-key: <key>` before changing credentials.
6. Verify domain mapping:
   - Prefer domains returned by `/api/application.one?applicationId=<id>`; `/api/domain.all?applicationId=...` may not exist on some Dokploy versions.
   - If missing, add with `POST /api/domain.create` then redeploy.
7. Verify public response:
   - `curl`/`fetch` target domain with browser-like User-Agent if Cloudflare blocks default clients.
   - expect `200` and expected HTML marker, not Dokploy 404 page.
   - Check for forbidden old markers too; success requires new marker present and stale marker absent.
8. Report without hedging when API and public checks pass. Avoid “if auto-deploy” once Dokploy source/domain/env/deploy/public response have been verified.

## Known Failure Patterns And Fixes

### 0) New static route or asset 404 after successful deploy
- Cause:
  - Dockerfile/image build context copied only a fixed file allowlist (for example `COPY package.json server.js index.html ... ./`) and omitted new root files such as `seo-machine.html`.
  - New directories such as `assets/` may also be omitted even when rendered HTML references them.
  - Repo push and Dokploy deploy succeeded, homepage/article HTML may update, but direct URL for new file or image returns app-level `404 Not found`.
- Fix:
  - Inspect `Dockerfile`/build config before blaming DNS/domain.
  - Add new static root files or directories to `COPY` allowlist, e.g. `COPY assets ./assets`, or change to a safe broader copy pattern.
  - Ensure the app server returns correct content types for new assets (`.png`, `.svg`, `.webp`, etc.) when using a custom Node static server.
  - Commit, push, redeploy.
  - Verify target URL repeatedly for 30-90s; first poll can hit old container/cache during rollout.

### 1) `404 page not found` on custom domain
- Cause:
  - Domain not attached to app
  - Attached but not redeployed yet
  - DNS points elsewhere
- Fix:
  - Add domain (`/api/domain.create`)
  - Redeploy app (`/api/application.deploy`)
  - Confirm DNS A/AAAA to correct server/proxy
  - Re-test `https://domain`

### 2) Deploy works but wrong code is live
- Cause:
  - App still linked to old repo/branch
  - Correct repo was pushed, but Dokploy app was not redeployed or rollout/cache still serves old container/page.
- Fix:
  - Update git provider source to correct owner/repo/branch
  - Redeploy with `POST /api/application.deploy` after push; do not assume GitHub push alone updated production unless auto-deploy is verified.
  - Poll public URL with cache-busting query and both positive/negative markers (`new CTA/link present`, `old CTA/link absent`).
  - If app status is `done` but old HTML remains, report deploy triggered and note likely rollout/cache delay; re-check after 1-3 minutes or clear CDN cache if configured.
  - Confirm latest commit visible in rendered page

### 3) Domain exists but app still unreachable / bad gateway
- Cause (per Dokploy docs):
  - Port mismatch between app runtime and domain port
  - App binding to `127.0.0.1` instead of `0.0.0.0`
- Fix:
  - Ensure runtime port matches domain target
  - Ensure service listens on `0.0.0.0`
  - Redeploy

### 4) Cert/HTTPS issue after adding domain
- Cause:
  - Domain added before DNS fully pointed to server
- Fix:
  - Point DNS first
  - Recreate domain mapping or restart routing layer as needed
  - Redeploy and verify HTTPS

### 5) Template/Compose service domain changes not taking effect
- Cause:
  - Domain changes in labels need redeploy
- Fix:
  - Always redeploy after domain add/remove/change for template/compose services

### 6) CLI/job image deploys `done` but is not web-usable
- Pattern:
  - Dokploy `applicationStatus` is `done`, source repo/build exists, but there are no domains/ports and the container command is a one-shot CLI command such as `--help`.
  - Example: `CMD ["python", "-m", "evolution.skills.evolve_skill", "--help"]` prints help and exits; build/deploy succeeds but no browser UI/API exists.
- Fix:
  - Treat it as a one-off job/CLI image, or add a long-running web/API server (`uvicorn`, `node server.js`, worker loop with health endpoint).
  - Attach a domain only after the service listens on `0.0.0.0:<port>` and exposes `/healthz`.
  - Report build status separately from web usability.

### 7) `Internal Server Error` after DNS starts resolving
- Pattern:
  - Dokploy deployment is `done`, Traefik routes to `http://<app>:<port>`, domain resolves, but `/` returns HTTP 500.
  - Build was successful, so suspect runtime env or middleware/init code, not Nixpacks build.
- Fast triage:
  - Hit `/`, `/login`, and one API route. If all return 500, inspect global middleware/proxy first.
  - Print only env key names from `/api/application.one`; check for missing runtime env such as `NEXT_PUBLIC_SUPABASE_URL`, `NEXT_PUBLIC_SUPABASE_ANON_KEY`, `CRON_SECRET`, provider keys.
  - Compare local `npm run build` with runtime-only code paths: Next middleware/proxy can crash at request time even when build passes.
- Fix options:
  - Preferred when app should run in demo/no-auth mode: guard middleware/client creation when env is absent and return `NextResponse.next()` instead of calling SDK constructors with `undefined`.
  - Preferred for production auth: set the missing runtime env in Dokploy app env (not only build secrets), redeploy, then re-test.
  - For Supabase SSR middleware, do not call `createServerClient(supabaseUrl!, supabaseKey!, ...)` unless both values exist.

## Safe Execution Rules
- Use only credentials/roles the user granted (GitHub CLI/session, Dokploy API/provider). Do not attempt SSH/root/server password access unless the user explicitly provided it for this task.
- Never print secrets (`DOKPLOY_API_KEY`, GH tokens).
- Never print raw `/api/gitProvider.getAll` or full `/api/application.one` responses: Dokploy may return provider secrets/private keys. Parse and print only masked/selected fields.
- Do not hedge with “if auto-deploy is connected” when Dokploy credentials exist. Verify via API and state exact source/domain/env/deploy status.
- Prefer idempotent API calls and re-check after each step.
- Keep audit trail:
  - app id
  - source repo/branch
  - env keys present (names only; no values)
  - domain list with host/path/port/https
  - deploy trigger time/API result
  - final HTTP check result and expected content markers

## References

- `references/static-file-docker-copy-allowlist.md` — new static URL returns 404 after successful deploy because Dockerfile copied only a root-file allowlist.
- `references/auth-header-and-assets.md` — Dokploy auth header retry pattern (`Authorization` vs `x-api-key`) and static asset deployment checks.
- `references/nixpacks-nextjs-and-log-limits.md` — switching Next.js apps from Dockerfile to Nixpacks, required `saveBuildType` null fields, Nixpacks env commands, and API log-access limits.
- `references/nixpacks-node-version-and-lockfile.md` — fixes for `npm ci` lockfile mismatch, Next.js Node 20+/24 Nixpacks selection, and duplicate Node/corepack conflicts.
- `references/nixpacks-nextjs-runtime-500.md` — deploy `done` but live site returns 500 due missing runtime env/middleware SDK init.
- `references/hermes-webui-agent-and-cloudflare-9router.md` — Hermes WebUI single-container Dokploy fixes: vendored agent import, aarch64 optional dependency pitfalls, WebUI onboarding bootstrap, `application.readLogs` query shape, and Cloudflare/9Router `/v1/*` block debugging.
- `references/hermes-webui-agent-runtime.md` — Hermes WebUI on Dokploy: vendored agent, aarch64 `hermes-agent[all]`/`mistralai` resolver failure, `/opt/hermes` ownership, `application.readLogs`, and 9Router/Cloudflare 1010 separation.
- `references/nixpacks-python-fastapi-9router.md` — Nixpacks deploy pattern for Python/FastAPI apps using 9Router, no local Docker required.
- `references/hermes-agent-self-evolution-nixpacks.md` — Nixpacks notes for deploying NousResearch/hermes-agent-self-evolution as a Python job/worker; upstream has no long-running web start command.
- `references/hermes-webui-dokploy-auth-2026-05-12.md` — Hermes WebUI password-auth deploy on Dokploy: branch `master` pitfall, required tRPC body fields, port 8787 domain mapping, Cloudflare DNS, and GET-based auth verification.
- `references/dokploy-cli-tool-deployed-not-web.md` — CLI/job image can show Dokploy `done` while not being browser-usable; verify long-running server, domain, port, and health endpoint separately.
- `references/static-site-contact-cta-redeploy.md` — fixing a static site contact/email CTA, pushing GitHub, explicitly redeploying Dokploy, and verifying around CDN/rollout delay.

## Ready-to-Run Verification Checklist
- [ ] Correct app id found in expected project/environment via `GET /api/project.all`
- [ ] App details checked via `GET /api/application.one?applicationId=<id>`
- [ ] Repo source points to target owner/repo + branch + buildPath
- [ ] Runtime env keys checked by presence/name only (for example `NODE_ENV`, `PORT`), no values/secrets printed
- [ ] Domain(s) attached to app with expected host/path/port/https
- [ ] GitHub provider source re-saved when needed via `POST /api/application.saveGithubProvider`
- [ ] Deploy triggered successfully via `POST /api/application.deploy` (HTTP 200)
- [ ] Latest deployment status checked when available; do not trust local `git push` alone
- [ ] Public domain returns `200`
- [ ] Response body contains expected app content markers (not Dokploy fallback 404)
- [ ] If Cloudflare Email Protection is enabled, `mailto:`/raw email may be rewritten to `/cdn-cgi/l/email-protection`; verify expected page markers plus absence of old email instead of failing on raw email absence
