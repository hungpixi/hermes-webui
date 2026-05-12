# hungpixi Cronjob Dokploy Pattern — 2026-05-10

Use for migrating Hermes/local cron-like work to Dokploy-managed services.

## User preferences learned

- Use many independent subagents when useful, but do not depend on `ds2api`.
- Treat `ds2api` / DeepSeek V4 as slow, free, web-search-strong background research.
- Continue deterministic work while slow search runs.
- Reserve `hungpixi/cronjob` and Dokploy project `cronjob` for Hermes/workflow cronjob services only.

## Local Hermes cron vs Dokploy cron service

Hermes cronjobs are local/Hermes-native and keep chat delivery/tool/memory behavior. They do not automatically run on Dokploy unless Hermes itself runs there.

Recommended pattern for durable jobs:

```txt
Hermes local = design/debug/write jobs
Dokploy cron service = scheduled execution
Reports/webhook = delivery
```

## Implemented service shape

Repo:

```txt
D:/code/cronjob
https://github.com/hungpixi/cronjob
```

Dokploy:

```txt
project: cronjob
app: cronjob
applicationId: U76_roahEZF5P6kANvoC5
```

Files:

```txt
Dockerfile
package.json
src/index.mjs
src/jobs/resource-miner.mjs
README.md
```

Default schedule:

```txt
21:10 Asia/Ho_Chi_Minh
```

Env:

```env
TZ=Asia/Ho_Chi_Minh
GITHUB_OWNER=hungpixi
GITHUB_TOKEN=optional_token_for_private_repos
RESOURCE_MINER_HOUR=21
RESOURCE_MINER_MINUTE=10
RUN_ON_START=true
REPORT_DIR=/tmp/cronjob-reports
REPORT_WEBHOOK_URL=optional_webhook
```

Local validation:

```bash
cd D:/code/cronjob
npm run check
npm run run:resource-miner
```

Dokploy API sequence that worked:

```txt
POST /api/project.create
body: {"name":"cronjob","description":"Hermes/workflow cronjob service"}

POST /api/application.create
body: {"name":"cronjob","environmentId":"<environmentId>","description":"Cronjob service"}

GET /api/gitProvider.getAll
pick github.githubId from provider

POST /api/application.saveGithubProvider
body: {
  "applicationId":"<applicationId>",
  "owner":"hungpixi",
  "repository":"cronjob",
  "branch":"main",
  "buildPath":"/",
  "githubId":"<github.githubId>",
  "triggerType":"push",
  "watchPaths":[]
}

POST /api/application.update
body: {"applicationId":"<applicationId>","env":"KEY=value\n..."}
worked for app env updates when `/api/application.saveEnvironment` required extra fields.

POST /api/application.deploy
body: {"applicationId":"<applicationId>"}

GET /api/deployment.all?applicationId=<applicationId>
expect latest status `done`
```

## Pitfalls

- If only deploying a normal app to Dokploy, Hermes cronjob state does not move with it.
- To keep Hermes-native cron behavior on Dokploy, run full Hermes runtime + scheduler + skills/memory/secrets there.
- If `GITHUB_TOKEN` missing, GitHub public repo metadata works but private repo contents are limited.
- If a user pastes a broad/non-expiring PAT, avoid echoing it, set only into Dokploy env if explicitly instructed, and recommend rotating to a selected-repo read-only token (`Contents: Read-only`, `Metadata: Read-only`).
- Verify GitHub token ability without printing it by fetching private repo metadata/content and reporting only status booleans.
- If no webhook is configured, reports live in container/logs only; add `REPORT_WEBHOOK_URL` for push delivery.
- Do not store raw secrets in `D:/code/cronjob` or committed files.
