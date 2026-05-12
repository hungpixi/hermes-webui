---
name: use-9router-default
description: Use when configuring, testing, debugging, or documenting Codex, Claude Code, Cursor, Cline, OpenClaw, or any OpenAI-compatible tool to route through user's default 9Router deployment at 9router.phamphunguyenhung.com.
---

# Use 9Router Default

## Overview

Use the user's 9Router deployment as the default OpenAI-compatible API gateway.

```text
Base URL: https://9router.phamphunguyenhung.com/v1
Dashboard: https://9router.phamphunguyenhung.com/dashboard
Default smoke-test model: cx/gpt-5.4
```

For DS2API-specific model selection, benchmarks, vision/search quirks, or cheap high-quota coding tasks, also use `use-ds2api-9router`.

Never print, commit, or paste API keys, dashboard passwords, provider tokens, backup JSON contents, or `db.json` contents.

## Tool Configuration

For OpenAI-compatible clients, set:

```text
Endpoint / Base URL: https://9router.phamphunguyenhung.com/v1
API Key: use a key from the 9Router dashboard
Model: cx/gpt-5.4 unless the user asks for another model
```

If a client expects an OpenAI API key environment variable, use the dashboard key with that client's normal variable name. Do not invent or reveal the key.

For this user, first inspect existing env variables and secret-file conventions before asking for keys. Prefer `~/AppData/Local/hermes/secrets/user.env` as the durable ledger, and keep adding project-specific 9Router/base-url mappings there over time. Do not ask again for the stable 9Router base URL or known env values. Support `*_FILE` paths and platform secret mounts such as `/run/secrets/<NAME>` when building apps. The user dislikes being asked to repeat secrets that already exist in env/path.

## Verification

Use `rtk` for shell commands when working in the user's 9Router repo.

Check TLS and dashboard:

```powershell
rtk proxy curl.exe -sSI https://9router.phamphunguyenhung.com/login
rtk proxy curl.exe -sSI https://9router.phamphunguyenhung.com/dashboard
```

Expected:

```text
/login      -> 200 OK
/dashboard  -> 307 redirect to /login when not signed in
```

Check API model listing without exposing secrets:

```powershell
rtk proxy node --input-type=module -e "const r=await fetch('https://9router.phamphunguyenhung.com/v1/models'); console.log('status', r.status); const j=await r.json(); console.log('models', Array.isArray(j.data)?j.data.length:'n/a'); console.log('first', Array.isArray(j.data)?j.data.slice(0,3).map(x=>x.id).join(', '):'n/a');"
```

For chat completion tests, require the user to provide a safe key source or use an already-local nonprinted key. Print only status, error shape, and returned content.

## Known Good State

As of 2026-05-03, this deployment was verified with:

```text
DNS: Cloudflare proxied
TLS: valid for normal curl and Node fetch
/v1/models: 200 OK
/v1/chat/completions with API key and model cx/gpt-5.4: 200 OK, returned "OK"
```

## Troubleshooting

If TLS fails with `self-signed certificate`, check Cloudflare first:

```text
DNS record: A 9router -> Dokploy IPv4
Proxy status: Proxied / orange cloud
SSL/TLS mode: Full
```

If `/v1/chat/completions` returns `401 Missing API key`, the endpoint is alive and `REQUIRE_API_KEY=true`; configure the client with a dashboard API key.

If `/v1/models` or `/v1/chat/completions` returns `403 Forbidden` even with a local key present, treat it as gateway/WAF/domain access, not proof that the key is bad. Check Cloudflare/Dokploy access rules, allowed hosts/IPs, and whether the remote API routes are publicly exposed. Also test any local gateway URL separately; a local `404` on `/models` means that local service is not serving OpenAI-compatible model listing at that path.

When adding 9Router fallback to an app that already supports OpenAI-compatible clients, prefer env aliases rather than new client code: `OPENAI_API_KEY || NINEROUTER_KEY` and `OPENAI_BASE_URL || NINEROUTER_REMOTE_URL || NINEROUTER_URL`.

If public URL returns `502`, use the `deploy-9router-dokploy` skill and check Dokploy deployment status, running container, app port `20128`, domain port, and the single `/data` volume.

If dashboard asks for a password, it is the 9Router dashboard password from the imported backup or current DB. The backup stores a bcrypt hash, not plaintext.
