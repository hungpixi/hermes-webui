# Nixpacks Node.js version and lockfile deploy failures

Use when Dokploy Nixpacks deploy fails during setup/install for Next.js/Node apps.

## Symptoms

### `npm ci` lock mismatch

```text
npm ci can only install packages when your package.json and package-lock.json or npm-shrinkwrap.json are in sync
Missing: @emnapi/core@... from lock file
Missing: @emnapi/runtime@... from lock file
```

Fix:
1. On workstation repo, run:
   ```bash
   npm install
   npm ci
   npm run build
   ```
2. Commit `package-lock.json` plus any needed `package.json` changes.
3. Push to repo Dokploy pulls from.
4. Redeploy.

Do not bypass with `npm install` in production unless user accepts non-reproducible deploys. Prefer fixing lockfile.

## Node version selection

Next.js 16 and modern deps require Node 20+; Nixpacks may default to Node 18 unless guided. Add to `package.json`:

```json
"engines": {
  "node": ">=20.19.0"
}
```

For Dokploy app env, prefer one version selector only:

```env
NODE_VERSION=24
NIXPACKS_INSTALL_CMD=npm ci
NIXPACKS_BUILD_CMD=npm run build
NIXPACKS_START_CMD=npm start
```

## Pitfall: conflicting Nix packages

Avoid combining `NODE_VERSION=24` with `NIXPACKS_PKGS=nodejs_20 npm-10_x` or similar. Nixpacks can install two Node versions:

```text
setup │ nodejs_20, npm-10_x, nodejs_24, npm-9_x
pkgs.buildEnv error: two given paths contain a conflicting subpath:
...nodejs-24.../bin/corepack and ...nodejs-20.../bin/corepack
```

Fix:
- Remove `NIXPACKS_PKGS`, `NIXPACKS_NODE_VERSION`, and old `NODE_VERSION` duplicates.
- Keep only one Node selector (`NODE_VERSION=24` or package `engines`).
- Redeploy.

## Verification

Poll deployment status via `deployment.allByType` until latest status is `done` or `error`. After `done`, test public domain separately; DNS failures are separate from deploy success.
