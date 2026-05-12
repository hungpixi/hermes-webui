# Hermes WebUI on Dokploy

Session learning from deploying `hungpixi/hermes-webui` to `hermes.phamphunguyenhung.com`.

## App shape

- WebUI is a Dockerfile app exposing port `8787`.
- Auth can be enabled with `HERMES_WEBUI_PASSWORD`.
- It stores sessions/settings/state under `HERMES_WEBUI_STATE_DIR` (default style: `~/.hermes/webui`).
- It needs Hermes Agent source importable as `run_agent.AIAgent`; otherwise chat returns:

```text
AIAgent not available -- check that hermes-agent is on sys.path
HERMES_WEBUI_AGENT_DIR: (not set)
sys.path entries mentioning hermes/agent: - /.hermes/hermes-agent
```

## Dokploy checklist

1. Verify GitHub provider uses actual repo branch. `nesquena/hermes-webui` default is `master`, not `main`; wrong branch can deploy stale image while app status still says `done`.
2. Save env with masked output only:

```env
HERMES_WEBUI_HOST=0.0.0.0
HERMES_WEBUI_PORT=8787
HERMES_WEBUI_PASSWORD=[REDACTED]
HERMES_WEBUI_SESSION_TTL=604800
HERMES_WEBUI_STATE_DIR=/home/hermeswebui/.hermes/webui
HERMES_WEBUI_DEFAULT_WORKSPACE=/workspace
HERMES_HOME=/home/hermeswebui/.hermes
HERMES_CONFIG_PATH=/home/hermeswebui/.hermes/config.yaml
HERMES_WEBUI_DEFAULT_MODEL=cx/gpt-5.5
NINEROUTER_URL=https://9router.phamphunguyenhung.com/v1
NINEROUTER_KEY=[REDACTED]
OPENAI_BASE_URL=https://9router.phamphunguyenhung.com/v1
OPENAI_API_KEY=[REDACTED]
```

3. Do not assume `applicationStatus: done` means chat works. Test through authenticated WebUI API:
   - `POST /api/auth/login`
   - `POST /api/session/new`
   - `POST /api/chat/start`
   - `GET /api/session?session_id=...`
4. If `/api/session` shows assistant message with `AIAgent not available`, fix agent source mount/install, not provider config.
5. If public `/health` becomes `502`, first revert risky Dockerfile/volume changes to restore service before further debugging.

## Persistent state note

Hermes WebUI is file-state/sqlite style, not a Postgres app by default. On Dokploy, prefer a persistent volume/mount for:

```text
/home/hermeswebui/.hermes
```

Seed or mount there:

```text
/home/hermeswebui/.hermes/hermes-agent
/home/hermeswebui/.hermes/config.yaml
/home/hermeswebui/.hermes/webui/
```

## API probes

Login and create session:

```bash
curl -k -sS -c /tmp/hermes_cj.txt \
  -X POST https://hermes.phamphunguyenhung.com/api/auth/login \
  -H 'Content-Type: application/json' \
  --data '{"password":"[REDACTED]"}'

sid=$(curl -k -sS -b /tmp/hermes_cj.txt \
  -X POST https://hermes.phamphunguyenhung.com/api/session/new \
  -H 'Content-Type: application/json' \
  --data '{"workspace":"/workspace"}' \
  | python -c "import sys,json; print(json.load(sys.stdin)['session']['session_id'])")
```

Start chat and inspect final session:

```bash
curl -k -sS -b /tmp/hermes_cj.txt \
  -X POST https://hermes.phamphunguyenhung.com/api/chat/start \
  -H 'Content-Type: application/json' \
  --data "{\"session_id\":\"$sid\",\"message\":\"reply exactly OK\",\"workspace\":\"/workspace\",\"model\":\"cx/gpt-5.5\"}"

curl -k -sS -b /tmp/hermes_cj.txt \
  "https://hermes.phamphunguyenhung.com/api/session?session_id=$sid"
```
