# Hermes WebUI on Dokploy: agent/runtime pitfalls

Use when a `nesquena/hermes-webui` Dokploy app deploys but chat fails or the route returns 502.

## Symptoms

- Public app works/auth works, but chat message returns:
  - `AIAgent not available -- check that hermes-agent is on sys.path`
  - `HERMES_WEBUI_AGENT_DIR: (not set)` or only `/.hermes/hermes-agent` appears in `sys.path`.
- Dokploy `applicationStatus` may be `done` while Cloudflare/domain returns `502`.
- Runtime logs show dependency install failure:
  - `No solution found ... mistralai ... hermes-agent[all]`
  - or `error: could not create 'hermes_agent.egg-info': Permission denied`.

## Durable fixes

1. Read runtime logs with GET TRPC, not POST:

```python
url = f"{DOKPLOY_BASE_URL}/api/trpc/application.readLogs?input=" + quote(json.dumps({"json":{"applicationId": app_id}}))
req = Request(url, headers={"x-api-key": DOKPLOY_API_KEY})
log = json.loads(urlopen(req).read())["result"]["data"]["json"]
```

2. Vendor Hermes Agent into the image or mount it into the container.
   For single-container Dokploy, a Dockerfile pattern:

```dockerfile
RUN git clone --depth=1 https://github.com/NousResearch/hermes-agent.git /opt/hermes \
    && chown -R hermeswebui:hermeswebui /opt/hermes
ENV HERMES_WEBUI_AGENT_DIR=/opt/hermes
ENV HERMES_HOME=/home/hermeswebui/.hermes
```

3. Avoid installing `hermes-agent[all]` on aarch64 if `mistralai` resolution fails. Patch `docker_init.bash` to install editable base package instead:

```bash
uv pip install -e "$_agent_src" --trusted-host pypi.org --trusted-host files.pythonhosted.org || error_exit "Failed to install hermes-agent base package"
```

4. If editable install fails with `hermes_agent.egg-info: Permission denied`, ensure the runtime user owns the vendored agent path:

```dockerfile
chown -R hermeswebui:hermeswebui /opt/hermes
```

5. Persist/bootstrap env/config as needed:

```text
HERMES_WEBUI_AGENT_DIR=/opt/hermes
HERMES_HOME=/home/hermeswebui/.hermes
HERMES_CONFIG_PATH=/home/hermeswebui/.hermes/config.yaml
HERMES_WEBUI_STATE_DIR=/home/hermeswebui/.hermes/webui
HERMES_WEBUI_DEFAULT_WORKSPACE=/workspace
```

## Verification gates

- `/health` returns `200`.
- Login works.
- `POST /api/session/new` works.
- `POST /api/chat/start` no longer returns `AIAgent not available`.
- If chat returns `Your request was blocked`, test upstream OpenAI-compatible gateway directly. Cloudflare 1010 on `/v1/chat/completions` is a gateway/WAF issue, not WebUI import failure.

## Pitfalls

- Dokploy `applicationStatus: done` is not enough; container can exit after entrypoint failure and public route returns `502`.
- Do not confuse Hermes WebUI file state with needing Postgres. It can use `.hermes` file state; missing agent/import is separate from session storage.
- If `NINEROUTER_URL` points to an internal localhost value, prefer public `NINEROUTER_REMOTE_URL` for remote containers.
- Do not print API keys/passwords while saving or verifying env.
