# Static Site Contact CTA Redeploy Notes

Scenario: user reports a static personal site contact/email button fails and asks to fix GitHub file then redeploy on Dokploy.

Useful workflow:
1. Identify actual GitHub repo from the domain, not from nearby similarly named projects. If local repos do not include the site, use `gh repo list <owner> --json name,url,description,homepageUrl` and search for domain/name markers.
2. Clone/pull target repo, search for `mailto:`, `contact`, `Liên hệ`, `zalo`, phone number, and CTA labels.
3. Prefer replacing the primary broken `mailto:` CTA with a durable external contact link when the user gives one, e.g. `https://zalo.me/<phone>` with `target="_blank" rel="noopener"`. Keep email as fallback if useful.
4. Commit and push to the production branch.
5. Trigger Dokploy redeploy explicitly through API when credentials exist:
   - `GET /api/project.all`, find app by `project -> environment -> applications[].name`.
   - `POST /api/application.deploy` with `{ applicationId }`.
   - Use `x-api-key` if that is the proven auth header for the install.
6. Verify public page with cache-busting query plus positive/negative markers. Example: new `zalo.me/<phone>` present and old `Gửi email` absent.

Pitfalls:
- Nearby projects may contain the same email/domain but are not the production `phamphunguyenhung.com` repo; user correction should redirect to the named domain repo first.
- `GET /api/application.one?applicationId=...` may return 404 on some Dokploy versions even though `project.all` includes the app and `/api/application.deploy` works.
- Cloudflare/Dokploy rollout can keep old HTML visible briefly after app status is `done`; poll for 1-3 minutes and report exact state instead of claiming public verification passed early.
- Never print `DOKPLOY_API_KEY` or full API payloads; selected app name/id/status only.
