---
name: deploy-9router-dokploy
description: Use when deploying, repairing, or documenting 9Router on Dokploy, especially repo hungpixi/9router, Nixpacks, port 20128, `/data` volume, and login/dashboard verification.
---

# Deploy 9Router On Dokploy

## Use When
- Target service is 9Router.
- Need deploy/repair/verify 9Router on Dokploy.

## Do Not Use When
- Generic Dokploy app issue not tied to 9Router. Use `dokploy-deploy-troubleshooter`.

## Known-Good Shape
- Repo: `https://github.com/hungpixi/9router.git`
- Build type: `Nixpacks`
- Build path: `/`
- Port: `20128`
- Volume: exactly one mount `/data`
- Domain: `9router.<domain>`

## Required Env (Core)
- `NODE_ENV=production`
- `PORT=20128`
- `HOSTNAME=0.0.0.0`
- `DATA_DIR=/data`
- `BASE_URL=https://9router.<domain>`

## Happy Path
1. Verify source/build fields match known-good shape.
2. Verify single `/data` volume, remove duplicate `/app/data` mounts.
3. Deploy and stream logs.
4. Verify container running.
5. Verify public endpoints:
   - `/login` -> `200`
   - `/dashboard` -> `307` redirect to `/login`.

## Useful Commands
- Inspect app:
  - `rtk proxy node scripts/dokploy/inspect-application-safe.mjs <appId>`
- Deploy + stream:
  - `rtk proxy node scripts/dokploy/deploy-and-stream-log.mjs <appId>`
- HTTP verify:
  - `rtk proxy curl.exe -skI https://9router.<domain>/login`

## Fallback Path
- If logs empty via websocket, poll deployment/API routes.
- If `502`, check in order: deploy status, container state, domain IP, domain port `20128`, volume duplication.

## Completion Gate
Claim running only when deploy `done`, container `running`, login/dashboard checks pass.

## Outputs
- Effective Dokploy config used (repo/build/port/volume/domain).
- Deployment + container runtime status.
- Public `/login` and `/dashboard` verification results.
