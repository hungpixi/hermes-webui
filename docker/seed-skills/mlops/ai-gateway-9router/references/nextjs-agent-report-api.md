# Next.js Agent Report API on 9Router

Session-derived pattern for building a SaaS dashboard agent runner that calls 9Router server-side and exports reports.

## Pattern

1. Keep provider secrets server-side only:
   - `NINEROUTER_URL`
   - `NINEROUTER_KEY`
   - task-specific `AGENT_MODEL`
2. Create a Node.js runtime API route, not Edge, when writing files:
   - `export const runtime = "nodejs";`
3. Call OpenAI-compatible endpoint:
   - `POST ${NINEROUTER_URL}/v1/chat/completions`
   - `Authorization: Bearer ${NINEROUTER_KEY}`
4. Save runtime report artifacts:
   - JSON for machine use
   - HTML for user-readable export
5. Return links to UI:
   - `/api/reports/:id`
   - `/api/reports/:id?format=html`
6. Include local fallback if 9Router call fails so dashboard flow does not break.

## Model discovery rule

Do not hardcode remembered model names. Always discover from the target router:

```bash
curl "$NINEROUTER_URL/v1/models" -H "Authorization: Bearer $NINEROUTER_KEY"
```

Then set a known available model, for example a discovered `cu/default` or another route from that exact list. In one session, `cx/sonar` returned 400 because it was not available on that router while `cu/default` was listed.

## Container/Dokploy pitfall

If a dashboard deployed on Dokploy falls back locally while local probes work, likely causes:

- `NINEROUTER_URL` points to laptop/localhost from the container perspective.
- 9Router is not exposed via public URL or same Docker/network.
- Router auth/model route differs between local and server env.

Fix by setting `NINEROUTER_URL` to an endpoint reachable from the Dokploy app container, or run 9Router on the same server/network. Then redeploy and verify with a server-side POST to the app endpoint.

## Minimal verification

```bash
curl -sS "$APP_URL/api/agent/run" \
  -H 'content-type: application/json' \
  -d '{"mode":"plan","topic":"SEO agent","url":"https://example.com","skills":["seo-blueprint-vi"]}'

curl -I "$APP_URL/api/reports/<id>?format=html"
```

Expected:

- `/api/agent/run` returns `ok: true`, `reportId`, `markdown`, `jsonUrl`, `htmlUrl`.
- `/api/reports/:id` returns JSON.
- `/api/reports/:id?format=html` returns `text/html`.
