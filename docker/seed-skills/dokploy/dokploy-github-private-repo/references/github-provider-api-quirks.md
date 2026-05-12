# Dokploy GitHub Provider API Quirks

Session learning from deploying `hungpixi/organic-lead-dashboard`.

## Project/app creation shape

- `POST /api/project.create` may return `true` or a partial response depending on instance/version. After create, re-read `/api/project.all` or `/api/project.one?projectId=...` to get authoritative IDs.
- `GET /api/project.one?projectId=<id>` returns environments and nested applications. Use this to recover `environmentId` and app IDs.
- `POST /api/application.create` worked with:

```json
{
  "name": "organic-lead-dashboard",
  "projectId": "<projectId>",
  "environmentId": "<environmentId>",
  "description": "..."
}
```

## API routing differences

This Dokploy instance accepts both public-style and tRPC-style routes for some resources:

- `GET /api/project.all` and `GET /api/trpc/project.all` both worked for listing projects.
- Procedure calls like `application.create`, `application.saveGitProvider`, `application.saveBuildType`, `application.saveEnvironment`, `domain.create`, and `application.deploy` worked via `/api/trpc/<procedure>` with body `{ "json": { ... } }`.
- `POST /api/project.create` worked via `/api/trpc/project.create` with body `{ "json": { "name": "...", "description": "..." } }`, returning both `project.projectId` and default `environment.environmentId`.
- If `project.all` shows no matching project, create project first, then create app with the returned `environmentId`.

## GitHub provider attachment

`/api/application.saveGithubProvider` requires `githubId` on this Dokploy instance. Without it, validation fails:

```text
fieldErrors.githubId: Invalid input: expected nonoptional, received undefined
```

Get provider ID from:

```text
GET /api/gitProvider.getAll
```

Use `item.github.githubId` when present. Do not print provider secrets/private keys from this endpoint; it may include sensitive nested GitHub app fields.

Known-good body:

```json
{
  "applicationId": "<applicationId>",
  "owner": "hungpixi",
  "repository": "organic-lead-dashboard",
  "branch": "main",
  "buildPath": "/",
  "githubId": "<item.github.githubId>",
  "triggerType": "push",
  "watchPaths": []
}
```

## Next.js Docker deploy

For Next.js standalone deploy:

- set `output: "standalone"` in `next.config.ts`
- Dockerfile should copy `.next/standalone`, `.next/static`, and `public`
- app port: `3000`
- commit and push Dockerfile changes before triggering Dokploy; Dokploy pulls GitHub state, not local working tree

Known-good Dockerfile:

```dockerfile
FROM node:20-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci

FROM node:20-alpine AS builder
WORKDIR /app
ENV NEXT_TELEMETRY_DISABLED=1
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:20-alpine AS runner
WORKDIR /app
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1
ENV PORT=3000
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
EXPOSE 3000
CMD ["node", "server.js"]
```

If using direct git URL (`application.saveGitProvider`) for private repo and deployments fail in <1s with `applicationStatus: error`, suspect clone auth/provider mismatch before debugging Docker.

## Domain attach

`POST /api/domain.create` worked with:

```json
{
  "host": "organic-lead-dashboard.phamphunguyenhung.com",
  "applicationId": "<applicationId>",
  "port": 3000,
  "path": "/",
  "https": true,
  "certificateType": "letsencrypt"
}
```

Then redeploy and verify public marker.
