---
name: dokploy-env-bootstrap-from-dcode
description: Bootstrap deploy env from trusted D:\\code sources, map to current repo, and validate via Dokploy API routes.
---

# Dokploy Env Bootstrap From D:\\code

Use this skill when target repo is missing deploy env and you need to auto-bootstrap Dokploy settings from trusted sources under `D:\\code`.

## Goal
- Build or refresh deploy env for current project without manual copy/paste secrets.
- Source from existing `D:\\code` repos (especially `auto-dokploy`) and adapt keys safely.
- Maintain `~/AppData/Local/hermes/secrets/user.env` as the durable shared env ledger; append/update project-specific keys there over time instead of treating it as fixed.
- When user asks to deploy/fix using stable env/base URLs, use `user.env` directly and do not repeatedly ask for values already present.
- Avoid printing sensitive values in logs.

## When To Trigger
- `.env`/`deploy/dokploy.env` missing.
- App deploys fail due to missing `DOKPLOY_BASE_URL`, `DOKPLOY_API_KEY`, repo/branch keys.
- Migrating site/repo and re-binding Dokploy app source.

## Source Discovery Order
1. Hermes user secret env: `%LOCALAPPDATA%\\hermes\\secrets\\user.env` / `~/AppData/Local/hermes/secrets/user.env` (preferred source on this workstation)
2. Process env already loaded in current shell
3. `D:\\code\\auto-dokploy\\deploy\\dokploy.env`
4. Any `D:\\code\\*\\deploy\\dokploy.env`
5. Any `D:\\code\\*\\.env*` containing both `DOKPLOY_BASE_URL` and `DOKPLOY_API_KEY`

If `DOKPLOY_BASE_URL` is missing from process env but Hermes user secret file exists, explicitly parse it inside scripts before calling Dokploy API. Log only candidate paths and key presence; never print values.

If multiple candidates exist, choose Hermes user secret env first. For legacy `D:\\code` candidates, choose most recently modified file with both required keys. Log only candidate paths and key presence; never print values.

## Secret Storage Policy
For this user, secrets and durable project env metadata must live in Hermes local secrets, not project repos. Treat `user.env` as a growing source of truth: add/update project-specific env keys when discovered, with clear prefixes, and keep raw values out of chat/logs.

```txt
~/AppData/Local/hermes/secrets/user.env
C:\\Users\\PhạmPhúNguyễnHưng\\AppData\\Local\\hermes\\secrets\\user.env
```

Allowed in repo folders:
- `.env.example`
- `deploy/*.env.example`
- docs with placeholders only

Not allowed in repo folders unless user explicitly overrides policy:
- `D:/code/.env`
- `<repo>/deploy/dokploy.env` containing raw secrets
- raw API tokens/keys in committed files

Mask values in output:

```txt
DOKPLOY_API_KEY=SET sk-...abcd
NINEROUTER_KEY=SET sk-...2d0f
```

## Required Keys (minimum)
- `DOKPLOY_BASE_URL`
- `DOKPLOY_API_KEY`
- `DOKPLOY_DEPLOY_MODE` (`github` provider flow preferred; fallback `custom-git`)
- `DOKPLOY_DEPLOY_REPOSITORY`
- `DOKPLOY_DEPLOY_BRANCH`

## Optional Keys (copy when present)
- `DOKPLOY_PROJECT_NAME`
- `DOKPLOY_ENVIRONMENT_NAME`
- `CONTROL_PLANE_DOMAIN`
- `CONTROL_PLANE_TOKEN`
- `WORKER_TOKEN`

## Mapping Rules
- Keep infra keys from source (`DOKPLOY_BASE_URL`, `DOKPLOY_API_KEY`).
- Override target repo keys:
  - `DOKPLOY_DEPLOY_REPOSITORY=<current owner/current repo>`
  - `DOKPLOY_DEPLOY_BRANCH=<current branch, default main>`
- Keep `DOKPLOY_DEPLOY_MODE=custom-git` only if GitHub provider route is unavailable.
- Never overwrite explicit local values unless user asked for full replace.

## Output Targets
- Preferred for shared secrets: `~/AppData/Local/hermes/secrets/user.env`.
- Preferred for repo mapping: shell exports or placeholder-only `deploy/*.env.example`.
- When user explicitly asks for project env, create `<current-repo>/env.local` from `~/AppData/Local/hermes/secrets/user.env`.
- Only create `<current-repo>/deploy/dokploy.env` with real secrets when user explicitly requests that exact per-project env file.
- If deploy folder missing and user requested `deploy/dokploy.env`: create `deploy/` and write file there.
- Never commit real secret env by default.

## Validation (must pass)
1. File exists at target path.
2. Required keys non-empty.
3. `DOKPLOY_DEPLOY_REPOSITORY` matches current remote repo.
4. API probe succeeds:
   - `GET /api/project.all` with `x-api-key`
5. If app update needed:
   - find app id via project/environment/app name
   - update source repo/branch
   - trigger deploy

## API-First App Binding Flow
1. `GET /api/project.all` -> locate target app (`project -> environment -> application`).
2. Update source:
   - preferred: `POST /api/application.saveGithubProvider`
   - fallback: `POST /api/application.saveGitProvider`
3. `POST /api/application.deploy`
4. Verify domain:
   - domain exists in app mapping
   - public URL returns 200 (not Dokploy 404 fallback)

## Security Rules
- Do not echo secret values.
- Do not print full env file content.
- Log only masked confirmation:
  - key present / missing
  - app id
  - repo/branch
  - deploy status

## Common Fixes
- `404 page not found`: attach domain to correct app, redeploy, verify DNS.
- Wrong code live: app still linked to old repo/branch; rebind source and redeploy.
- Deploy OK but app dead: port/bind mismatch; ensure service listens on `0.0.0.0` and expected port.
- WebUI deploy with agent import/config/onboarding problems: see `references/hermes-webui-dokploy.md`.

## App-specific references
- Hermes WebUI on Dokploy: `references/hermes-webui-dokploy.md` — auth env, 9Router config, agent-source import failure, and API probes.

## Validation Commands
Load shared env in Git Bash:

```bash
set -a
source "$HOME/AppData/Local/hermes/secrets/user.env"
set +a
```

Check env presence without printing secrets:

```bash
python - <<'PY'
import os
keys=['NINEROUTER_URL','NINEROUTER_KEY','DOKPLOY_BASE_URL','DOKPLOY_API_KEY','DOKPLOY_DEPLOY_MODE','DOKPLOY_DEPLOY_REPOSITORY','DOKPLOY_DEPLOY_BRANCH','CLOUDFLARE_ACCOUNT_ID','CLOUDFLARE_API_TOKEN']
for k in keys:
    v=os.environ.get(k)
    print(k, 'SET' if v else 'MISSING')
PY
```

Validate Dokploy API:

```bash
curl "$DOKPLOY_BASE_URL/api/project.all" -H "x-api-key: $DOKPLOY_API_KEY"
```

Validate 9Router when relevant:

```bash
curl "$NINEROUTER_URL/api/health"
curl "$NINEROUTER_URL/v1/models" -H "Authorization: Bearer $NINEROUTER_KEY"
```

Validate Cloudflare when relevant:

```bash
curl "https://api.cloudflare.com/client/v4/accounts/$CLOUDFLARE_ACCOUNT_ID/tokens/verify" \
  -H "Authorization: Bearer $CLOUDFLARE_API_TOKEN"
```

## Success Criteria
- Shared Hermes secrets file exists or current shell has required env.
- Required keys are present and masked in logs.
- Deploy repo/branch mapping matches current target.
- Dokploy API probe succeeds.
- If app update needed, Dokploy app source points to target repo/branch.
- Deploy triggered successfully when requested.
- Public domain serves expected page with HTTP 200.
