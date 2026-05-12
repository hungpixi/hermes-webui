# Hermes WebUI on Dokploy: agent import, aarch64 deps, and 9Router Cloudflare blocks

Use when deploying or repairing `nesquena/hermes-webui` (or fork) on Dokploy with a single Docker container and an OpenAI-compatible gateway such as 9Router.

## Symptoms

- Public app health is `502` even Dokploy application status says `done`.
- Chat shows `AIAgent not available -- check that hermes-agent is on sys.path`.
- Chat reaches provider but returns `HTTP 403: Your request was blocked` while direct local API test may work.
- Onboarding/setup appears again after redeploy because state is not persisted or bootstrapped.

## Durable fixes

1. Include Hermes Agent source inside the image or mount it into container:

```dockerfile
RUN git clone --depth=1 https://github.com/NousResearch/hermes-agent.git /opt/hermes \
    && chown -R hermeswebui:hermeswebui /opt/hermes
ENV HERMES_WEBUI_AGENT_DIR=/opt/hermes
ENV HERMES_HOME=/home/hermeswebui/.hermes
```

2. In `docker_init.bash`, do not install `hermes-agent[all]` on Dokploy/aarch64 if it fails resolving optional deps such as `mistralai`. Install editable base package instead:

```bash
uv pip install -e "$_agent_src" --trusted-host pypi.org --trusted-host files.pythonhosted.org \
  || error_exit "Failed to install hermes-agent base package"
```

3. If installing editable from `/opt/hermes`, ensure runtime user can write metadata:

```dockerfile
chown -R hermeswebui:hermeswebui /opt/hermes
```

Otherwise `uv pip install -e /opt/hermes` can fail with:

```text
error: could not create 'hermes_agent.egg-info': Permission denied
```

4. Bootstrap non-secret config at container start to avoid repeated setup screens:

```yaml
model:
  provider: custom
  default: cx/gpt-5.5
  base_url: https://9router.example.com/v1
providers: {}
fallback_providers:
- provider: custom
  model: cx/gpt-5.5
  base_url: https://9router.example.com/v1
agent:
  max_turns: 90
  gateway_timeout: 1800
```

Also seed WebUI settings:

```json
{
  "onboarding_completed": true,
  "api_redact_enabled": true
}
```

5. Use remote 9Router URL for app env; avoid internal/local URL unless 9Router is on same Docker network:

```text
OPENAI_BASE_URL=https://9router.example.com/v1
OPENAI_API_KEY=[REDACTED]
NINEROUTER_URL=https://9router.example.com/v1
NINEROUTER_KEY=[REDACTED]
```

## Cloudflare 1010 / 403 pattern

If 9Router sits behind Cloudflare and Hermes WebUI logs show:

```text
Provider: custom  Model: cx/gpt-5.5
Endpoint: https://9router.example.com/v1
HTTP 403: Your request was blocked.
```

Test direct:

```bash
curl -sS https://9router.example.com/v1/chat/completions \
  -H "Authorization: Bearer $NINEROUTER_KEY" \
  -H "Content-Type: application/json" \
  --data '{"model":"cx/gpt-5.5","messages":[{"role":"user","content":"reply exactly OK"}],"max_tokens":20}'
```

Create Cloudflare WAF/ruleset rule for API path. Prefer scoping to Dokploy server IP when possible:

```text
(http.host eq "9router.example.com" and starts_with(http.request.uri.path, "/v1/") and ip.src eq <dokploy_public_ip>)
```

Action: `Skip` current/custom/WAF/security products, or `Allow` if Skip options unavailable. If server still gets 403 after direct local test returns 200, also check Bot Fight Mode, Browser Integrity Check, Security Level, and IP reputation products; some require Configuration Rules, not only WAF custom rules.

## Verification gate

Do not claim done until all pass:

```text
GET /health -> 200
POST /api/auth/login -> logged_in true
POST /api/chat/start -> stream_id
GET /api/session?session_id=... -> assistant reply not AIAgent error and not Cloudflare block
9Router direct POST /v1/chat/completions -> 200 from expected client path
```

## Pitfalls

- Dokploy `applicationStatus=done` can mean build/deploy process finished, not that app is listening. Always hit `/health`.
- `application.readLogs` is a query endpoint; call it with GET `?input={"json":{"applicationId":"..."}}`, not POST.
- Cloudflare token that can edit DNS may not have Rulesets/WAF permission. Need Zone Read + Rulesets/WAF Edit (or equivalent UI permissions).
- Do not print raw provider tokens or passwords in logs or summaries.
