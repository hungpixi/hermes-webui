# 9Router image notes for SEO articles

Source skill loaded from:
https://raw.githubusercontent.com/decolua/9router/refs/heads/master/skills/9router-image/SKILL.md

## Required env
- `NINEROUTER_URL`
- `NINEROUTER_KEY` if auth enabled

Load from local secret file when shell env lacks them; do not print values.

## Discover models
```bash
curl "$NINEROUTER_URL/v1/models/image"
```
Observed models in one setup:
- `gemini/gemini-3.1-flash-image-preview`
- `gemini/gemini-3-pro-image-preview`
- `gemini/gemini-2.5-flash-image`
- `cx/gpt-5.4-image`
- `cx/gpt-5.3-image`
- `cx/gpt-5.2-image`

## Generate binary image
```bash
curl -X POST "$NINEROUTER_URL/v1/images/generations?response_format=binary" \
  -H "x-api-key: $NINEROUTER_KEY" \
  -H "Content-Type: application/json" \
  -d '{"model":"cx/gpt-5.4-image","prompt":"...","size":"1792x1024","output_format":"png"}' \
  --output assets/example.png
```
Some installs use `Authorization: Bearer`; use the upstream 9Router setup. Keep keys masked.

## SEO article image checklist
1. Prompt for useful diagram, not decoration.
2. Use short exact Vietnamese labels.
3. Run vision check for legibility and spelling.
4. Regenerate if Vietnamese text has typos.
5. Add `alt`, caption, `og:image`, Article JSON-LD `image`.
6. Ensure Docker/static server includes `assets/` and image MIME types.
