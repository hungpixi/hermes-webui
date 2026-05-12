# Dokploy: CLI Tool Deploys `done` But Is Not Web-Usable

## Pattern
A Dokploy app can show `applicationStatus: done` even though there is no browser-usable service.

Observed with `hungpixi/hermes-agent-self-evolution`:

```txt
project: hermes-agent-self-evolution
env: production
app: hermes-agent-self-evolution
status: done
sourceType: github
owner/repository/branch: hungpixi/hermes-agent-self-evolution/main
buildType: dockerfile
Dockerfile CMD: python -m evolution.skills.evolve_skill --help
domains: []
```

## Root Cause
The container is a CLI/job image, not a long-running HTTP server:

```dockerfile
CMD ["python", "-m", "evolution.skills.evolve_skill", "--help"]
```

This command prints help and exits. Dokploy build/deploy can complete, but there is:

- no listening port
- no domain mapping
- no health endpoint
- no UI/API to open in browser

## Triage Steps
1. Verify app details through Dokploy API/project list.
2. Inspect repository `Dockerfile`, `nixpacks.toml`, `Procfile`, or start command.
3. Check whether command is long-running (`uvicorn`, `node server.js`, worker loop) or one-shot (`--help`, migration, script).
4. Check `domains` and configured port.
5. If no domain/port and command exits, report: deploy is built, not web-usable.

## Fix Options
### Option A: Treat as job/CLI
Use Dokploy image for one-off commands or scheduled jobs, not browser UI.

Example:

```bash
python -m evolution.skills.evolve_skill --skill seo-blueprint-vi --iterations 5 --eval-source synthetic
```

### Option B: Convert to web/API service
Add a long-running server:

```txt
GET /healthz
POST /evolve-skill
GET /runs
GET /runs/{id}
```

Example Dockerfile command:

```dockerfile
CMD ["uvicorn", "evolution.api:app", "--host", "0.0.0.0", "--port", "3000"]
```

Then attach a domain and verify `/healthz`.

## Reporting Rule
Do not tell user “it works” only because Dokploy status is `done`. Say exactly:

- deployed/build status
- web usability status
- domain/port status
- next action to make it usable
