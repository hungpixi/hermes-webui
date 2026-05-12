---
name: dokploy-github-private-repo
description: Use when deploying GitHub repositories to Dokploy, especially private repos, Dokploy clone failures, "could not read Username for 'https://github.com'", "Cloning Repo Custom", DOKPLOY_DEPLOY_MODE, GitHub provider vs custom-git decisions, or when an app stopped deploying after a repo was made private.
---

# Dokploy GitHub Private Repo

## Rule

Never use Dokploy `custom-git` for a private GitHub repository.

If the repo is private, configure the app through a Dokploy GitHub provider. `custom-git` clones `https://github.com/OWNER/REPO.git` without injected credentials and commonly fails with:

```text
fatal: could not read Username for 'https://github.com': No such device or address
Cloning Repo Custom https://github.com/OWNER/REPO.git
```

Do repository cloning/copying with GitHub tooling (`gh repo clone`, `gh repo create --source`, `git remote`) on the workstation. Do not treat Dokploy as the repo cloning/migration tool. Dokploy should only be bound to an existing GitHub repo via provider, then deploy from that source.

## Workflow

1. Check repository visibility with GitHub CLI:

```powershell
rtk proxy gh repo view OWNER/REPO --json visibility,isPrivate,defaultBranchRef
```

2. Check Dokploy env and providers without printing secrets:

```powershell
rtk proxy node --input-type=module -e "/* load deploy env, call /api/gitProvider.getAll, print provider count/types only */"
```

Credentials paths on this workstation:

```text
D:\\code\\agents-agency\\deploy\\dokploy.env
C:\\Users\\PhạmPhúNguyễnHưng\\AppData\\Local\\hermes\\secrets\\user.env
```

Check both paths. `user.env` may be the active source when `deploy/dokploy.env` is absent. Expected keys: `DOKPLOY_BASE_URL`, `DOKPLOY_API_KEY`; 9Router deployments may also need `NINEROUTER_KEY`, `NINEROUTER_REMOTE_URL`, and `NINEROUTER_URL`. Never print or commit token values.

3. Choose source mode:

- Private GitHub repo: use Dokploy GitHub provider. Do not set `DOKPLOY_DEPLOY_MODE=custom-git`.
- Public GitHub repo: `custom-git` is acceptable, but GitHub provider is still safer when available.
- No GitHub provider and repo must stay private: stop and ask the user to connect GitHub provider in Dokploy, or use a deploy key/token strategy explicitly supported by the current Dokploy instance.

4. Configure private GitHub source with:

```text
POST /api/application.saveGithubProvider
{
  "applicationId": "<id>",
  "owner": "OWNER",
  "repository": "REPO",
  "branch": "master",
  "buildPath": "/",
  "githubId": "<github-provider-id>",
  "triggerType": "push",
  "watchPaths": []
}
```

Get `<github-provider-id>` from `/api/gitProvider.getAll`. Prefer `item.github.githubId`, fallback to `item.githubId` or `item.gitProviderId` only if Dokploy accepts it. Do not print full provider payloads: some Dokploy versions include GitHub app secrets/private keys in nested fields. See `references/github-provider-api-quirks.md` for known-good bodies and deployment/domain attach notes.

5. For this user's automation, prefer setting the app-specific env override:

```env
DOKPLOY_DEPLOY_MODE=github
```

This prevents a global `deploy/dokploy.env` value such as `DOKPLOY_DEPLOY_MODE=custom-git` from leaking into private-repo apps.

6. Re-apply the app after changing source mode:

```powershell
rtk npm run dokploy:claude:dry-run
rtk npm run dokploy:claude:apply
```

7. Verify deployment result:

```powershell
rtk proxy node -e "/* call /api/deployment.all?applicationId=<id>; require latest status done */"
```

Do not trust `applicationStatus` alone. It can be stale or misleading after a failed build.

## Next.js / Docker Follow-Up

See `references/github-provider-api-quirks.md` for tRPC route shapes, project/app creation details, and a known-good Next.js Dockerfile.

After source mode is fixed, if deployment still fails fast:

- Check Dockerfile copy paths. `COPY --from=builder /app/public ./public` requires a committed `public/` directory.
- For Next.js standalone, set `output: "standalone"` and serve port `3000`.
- Attach Dokploy domains to port `3000` unless the container serves another port.
- Commit and push Docker fixes before redeploy; Dokploy pulls from GitHub, not local files.

## Guardrails

- For this user's repo workflow, clone/manage GitHub repos with `gh`/git locally first; do not rely on Dokploy custom-git as the cloning mechanism for setup. Dokploy should receive a configured source (GitHub provider for private repos, or explicit public repo if user chose public).
- Do not make a private repo public just to fix Dokploy cloning unless the user explicitly chooses that tradeoff.
- Do not print GitHub tokens, Dokploy API keys, or provider secrets.
- Prefer `gh` for GitHub operations; clone/create/copy repositories with `gh`/git locally, then bind Dokploy to the finished GitHub repo through provider.
- Do not use SSH/root/server password attempts to inspect logs unless the user explicitly grants that access; with member/API access, use Dokploy UI/API and user-provided log excerpts.
- Use `rtk` for all shell commands when available.
- If logs still say `Cloning Repo Custom` after applying, the app is still configured as custom git; fix source configuration before debugging Docker build.
- If Cloudflare returns 502, check deployment status and container port before changing DNS.
