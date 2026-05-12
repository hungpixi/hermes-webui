# Dokploy Nixpacks for Next.js and Log Access Limits

Session learning from migrating a Next.js app from Dockerfile to Nixpacks on Dokploy.

## When to use Nixpacks

If Dockerfile deployment fails or user asks to avoid Docker, switch the Dokploy app build type to `nixpacks` instead of debugging Docker layers first.

Dokploy Core docs: Applications -> Build Type -> Nixpacks. Nixpacks is the default build type and is configured through `NIXPACKS_*` env vars in the application Environment tab.

## Known-good app update via tRPC

Use GitHub provider source first when repo is private:

```json
POST /api/trpc/application.saveGithubProvider
{
  "applicationId": "<app>",
  "owner": "hungpixi",
  "repository": "<repo>",
  "branch": "master",
  "buildPath": "/",
  "githubId": "<github-provider-id>",
  "triggerType": "push",
  "watchPaths": []
}
```

Then set build type:

```json
POST /api/trpc/application.saveBuildType
{
  "applicationId": "<app>",
  "buildType": "nixpacks",
  "publishDirectory": null,
  "dockerfile": null,
  "dockerContextPath": null,
  "dockerBuildStage": null,
  "herokuVersion": null,
  "isStaticSpa": null,
  "railpackVersion": null
}
```

This Dokploy instance's validator may require irrelevant build-type fields to be present as `null`; omitting them can trigger zod errors for non-selected build types.

## Next.js Nixpacks env

Set runtime env plus explicit Nixpacks commands:

```env
NODE_ENV=production
PORT=3000
HOSTNAME=0.0.0.0
NEXT_TELEMETRY_DISABLED=1
NIXPACKS_INSTALL_CMD=npm ci
NIXPACKS_BUILD_CMD=npm run build
NIXPACKS_START_CMD=npm start
```

For OpenAI-compatible 9Router fallback, set names only in logs/reports; do not print values:

```env
AI_PROVIDER=openai
NINEROUTER_REMOTE_URL=https://9router.phamphunguyenhung.com/v1
OPENAI_BASE_URL=https://9router.phamphunguyenhung.com/v1
NINEROUTER_KEY=<secret>
OPENAI_API_KEY=<same secret alias if app expects OpenAI key>
```

## Deploy and verify

Trigger:

```json
POST /api/trpc/application.deploy
{"applicationId":"<app>"}
```

Poll:

```json
GET /api/trpc/deployment.allByType?input={"json":{"id":"<app>","type":"application"}}
```

Do not trust `applicationStatus` alone; use latest deployment status.

## Log access caveat

Some Dokploy API installs expose deployment records with `logPath` but not a readable build-log endpoint through the API key. If deploy status becomes `error` and routes like `deployment.read`/`logs.read` return 404 or `application.readLogs` only reads running container logs, report exact `logPath` and ask user to open Dokploy UI logs. Do not attempt SSH/root unless user explicitly provided SSH credentials.

## DNS check

If deployment errors are fixed but public URL fails with `getaddrinfo failed`, treat it as DNS not resolving from the client, not an app runtime failure. Verify DNS separately before changing app source/build settings.
