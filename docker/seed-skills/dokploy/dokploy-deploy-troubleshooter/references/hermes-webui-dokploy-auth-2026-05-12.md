# Hermes WebUI on Dokploy: password auth + tRPC quirks

Session learning from deploying `nesquena/hermes-webui` fork as `hungpixi/hermes-webui` to `hermes.phamphunguyenhung.com`.

## App shape

- Upstream repo default branch is `master`, not `main`.
- Dockerfile exposes `8787` and health endpoint `/health`.
- WebUI password auth is enabled by `HERMES_WEBUI_PASSWORD`.
- Auth status endpoint:

```bash
curl -sS https://hermes.phamphunguyenhung.com/api/auth/status
```

Expected when protected:

```json
{
  "auth_enabled": true,
  "logged_in": false
}
```

## Dokploy tRPC bodies that worked

### `application.saveBuildType`

This instance requires nonoptional extra fields:

```json
{
  "applicationId": "<app>",
  "buildType": "dockerfile",
  "dockerfile": "Dockerfile",
  "dockerContextPath": "/",
  "dockerBuildStage": "",
  "herokuVersion": "",
  "railpackVersion": ""
}
```

### `application.saveEnvironment`

This instance requires nonoptional extra fields:

```json
{
  "applicationId": "<app>",
  "env": "HERMES_WEBUI_HOST=0.0.0.0\nHERMES_WEBUI_PORT=8787\nHERMES_WEBUI_PASSWORD=<secret>\nHERMES_WEBUI_SESSION_TTL=604800\nHERMES_WEBUI_STATE_DIR=/home/hermeswebui/.hermes/webui\nHERMES_WEBUI_DEFAULT_WORKSPACE=/workspace\n",
  "buildArgs": "",
  "buildSecrets": "",
  "createEnvFile": false
}
```

Never print the password. Store it in `~/AppData/Local/hermes/secrets/user.env`.

### `application.saveGitProvider`

For public repo/fork direct git:

```json
{
  "applicationId": "<app>",
  "customGitUrl": "https://github.com/hungpixi/hermes-webui.git",
  "customGitBranch": "master",
  "customGitBuildPath": "/",
  "enableSubmodules": false,
  "watchPaths": []
}
```

If deploy fails in under ~1s with `applicationStatus: error`, check branch/source before Docker. In this case `main` failed because repo default was `master`; switching to `master` fixed deploy.

### Domain attach

```json
{
  "host": "hermes.phamphunguyenhung.com",
  "applicationId": "<app>",
  "port": 8787,
  "path": "/",
  "https": true,
  "certificateType": "letsencrypt"
}
```

## DNS

Cloudflare proxied A record worked:

```txt
hermes.phamphunguyenhung.com -> Dokploy server IP
proxied: true
```

## Verification notes

`curl -I`/HEAD may return `501 Not Implemented` even when GET is healthy. Verify with GET:

```bash
curl -sS -o /dev/null -w 'health %{http_code} %{content_type}\n' https://hermes.phamphunguyenhung.com/health
curl -sS https://hermes.phamphunguyenhung.com/api/auth/status
curl -sS -o /dev/null -w 'login %{http_code} %{content_type}\n' https://hermes.phamphunguyenhung.com/login
curl -sS -o /dev/null -w 'root %{http_code}\n' https://hermes.phamphunguyenhung.com/
```

Expected:

```txt
health 200 application/json
/login 200 text/html
/ 302 to login
/api/auth/status auth_enabled=true logged_in=false
```
