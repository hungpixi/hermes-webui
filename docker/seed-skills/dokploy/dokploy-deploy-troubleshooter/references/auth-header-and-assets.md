# Dokploy API auth header and static assets notes

## API auth header quirk
Some Dokploy installs accept `Authorization: Bearer <key>`; others return `401` and require `x-api-key: <key>`.

Safe retry pattern:
1. Try the header known to work for current environment.
2. If `401`, retry with `x-api-key` before rotating credentials or changing secrets.
3. Never print the API key.

## Static assets after deploy
If article HTML references new assets and production image URLs return 404 while HTML is updated:
- Inspect Dockerfile/build allowlist.
- Add `COPY assets ./assets` or equivalent.
- Add content types in custom static server:
  - `.png` -> `image/png`
  - `.svg` -> `image/svg+xml; charset=utf-8`
  - `.webp` -> `image/webp`
  - `.jpg`/`.jpeg` -> `image/jpeg`
- Commit/push/redeploy.
- Verify direct asset URL returns `200` and `Content-Type: image/*`.
