# Hermes Agent Docker + Dokploy + Community UI notes

Use when deploying Hermes Agent and community web frontends on Dokploy/Traefik behind a subdomain.

## Upstream Docker facts

Official Hermes Agent Docker data model:

- Mount persistent agent state at `/opt/data`.
- First-time setup:

```bash
mkdir -p ~/.hermes
docker run -it --rm \
  -v ~/.hermes:/opt/data \
  nousresearch/hermes-agent setup
```

- Gateway mode:

```bash
docker run -d \
  --name hermes \
  --restart unless-stopped \
  -v ~/.hermes:/opt/data \
  -p 8642:8642 \
  nousresearch/hermes-agent gateway run
```

- Exposing API server needs:

```env
API_SERVER_ENABLED=true
API_SERVER_HOST=0.0.0.0
API_SERVER_KEY=<strong 8+ chars; prefer openssl rand -hex 32>
API_SERVER_CORS_ORIGINS=https://<public-host>
```

- Built-in dashboard side-process in same container:

```env
HERMES_DASHBOARD=1
HERMES_DASHBOARD_HOST=0.0.0.0
HERMES_DASHBOARD_PORT=9119
```

Expose `9119` only through internal network unless deliberately publishing dashboard.

## Dokploy shape

For public hosting, route public hostname to one UI only (usually `hermes-workspace:3000`). Keep gateway (`8642`) and dashboard API (`9119`) internal to Docker network where possible.

Security baseline:

- Set `API_SERVER_KEY` on `hermes-agent`.
- Pass same value as `HERMES_API_TOKEN` to workspace.
- Set `HERMES_PASSWORD` for workspace when `HOST=0.0.0.0`.
- Set `COOKIE_SECURE=1` and `TRUST_PROXY=1` behind HTTPS reverse proxy/Dokploy.
- Do not route raw gateway API to public internet unless user explicitly accepts risk.

## Compose template: Agent + Workspace

```yaml
services:
  hermes-agent:
    image: nousresearch/hermes-agent:latest
    restart: unless-stopped
    command: ["gateway", "run"]
    environment:
      API_SERVER_ENABLED: "true"
      API_SERVER_HOST: "0.0.0.0"
      API_SERVER_KEY: "${API_SERVER_KEY}"
      API_SERVER_CORS_ORIGINS: "https://${PUBLIC_HOST}"
      HERMES_DASHBOARD: "1"
      HERMES_DASHBOARD_HOST: "0.0.0.0"
      HERMES_DASHBOARD_PORT: "9119"
      ANTHROPIC_API_KEY: "${ANTHROPIC_API_KEY:-}"
      OPENAI_API_KEY: "${OPENAI_API_KEY:-}"
      OPENROUTER_API_KEY: "${OPENROUTER_API_KEY:-}"
      GOOGLE_API_KEY: "${GOOGLE_API_KEY:-}"
    volumes:
      - hermes-data:/opt/data
    expose:
      - "8642"
      - "9119"

  hermes-workspace:
    image: ghcr.io/outsourc-e/hermes-workspace:latest
    restart: unless-stopped
    depends_on:
      - hermes-agent
    environment:
      HERMES_API_URL: "http://hermes-agent:8642"
      HERMES_API_TOKEN: "${API_SERVER_KEY}"
      HERMES_DASHBOARD_URL: "http://hermes-agent:9119"
      HERMES_PASSWORD: "${HERMES_PASSWORD}"
      COOKIE_SECURE: "1"
      TRUST_PROXY: "1"
      HOST: "0.0.0.0"
      PORT: "3000"
    expose:
      - "3000"

volumes:
  hermes-data:
```

Dokploy route: `https://<host>` → `hermes-workspace` port `3000`.

## Optional: nesquena/hermes-webui

`hermes-webui` uses port `8787` and state under `/home/hermeswebui/.hermes/webui`. Its README Docker compose mounts:

```yaml
volumes:
  - ${HERMES_HOME:-${HOME}/.hermes}:/home/hermeswebui/.hermes
  - ${HERMES_WORKSPACE:-${HOME}/workspace}:/workspace
environment:
  HERMES_WEBUI_HOST: 0.0.0.0
  HERMES_WEBUI_PORT: 8787
  HERMES_WEBUI_STATE_DIR: /home/hermeswebui/.hermes/webui
  HERMES_WEBUI_PASSWORD: ${HERMES_PASSWORD}
  HERMES_SKIP_CHMOD: "1"
```

If deploying alongside workspace, give it a separate subdomain or do not expose it.

## Optional: super-hermes skills in container

Install into mounted Hermes data volume after agent volume exists:

```bash
docker exec -it <hermes-agent-container> bash -lc '
  cd /tmp &&
  git clone https://github.com/Cranot/super-hermes.git &&
  mkdir -p /opt/data/skills /opt/data/prisms &&
  cp -r super-hermes/skills/* /opt/data/skills/ &&
  cp -r super-hermes/prisms/* /opt/data/prisms/
'
```

Skills become `/prism-scan`, `/prism-full`, `/prism-3way`, `/prism-discover`, `/prism-reflect` after Hermes reload/restart.

## Verification

DNS/proxy:

```bash
nslookup <host>
curl -I -L --max-time 10 https://<host>
```

App health from server/network:

```bash
curl http://hermes-agent:8642/health
curl http://hermes-agent:9119/api/status
curl http://hermes-workspace:3000/
```

If public host returns `404 Not Found` from Cloudflare/Dokploy, DNS is likely OK but Dokploy route/app binding is missing or app has not been deployed.

## Dokploy env discovery in this user's setup

User keeps deploy credentials in `D:\code\user.env` with:

```env
DOKPLOY_BASE_URL=https://dp.sgp1.w9.nu
DOKPLOY_API_KEY=...
```

When user says "login vào Dokploy" or asks to deploy on Dokploy, first check this file and validate with a harmless API call before asking for browser credentials.
Do not print full secrets.
