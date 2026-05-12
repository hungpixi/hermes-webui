# 9Router Image Generation Logo Workflow

Session source: Organic Lead Engine dashboard logo generation, May 2026.

## Use case

Generate a product logo/mark through 9Router image models, save it into a Next.js/TailAdmin app, and verify it visually.

## Discovery

```bash
curl "$NINEROUTER_URL/v1/models/image"
```

Observed image models included:

- `gemini/gemini-3.1-flash-image-preview`
- `gemini/gemini-3-pro-image-preview`
- `gemini/gemini-2.5-flash-image`
- `cx/gpt-5.4-image`
- `cx/gpt-5.3-image`
- `cx/gpt-5.2-image`

## Generation endpoint

Use the 9Router image skill endpoint:

```txt
POST $NINEROUTER_URL/v1/images/generations
```

Useful payload:

```json
{
  "model": "cx/gpt-5.4-image",
  "prompt": "Professional SaaS logo icon... no text... blue gradient... AI agent motif...",
  "size": "1024x1024",
  "n": 1,
  "response_format": "b64_json"
}
```

Save response:

- If `data[0].b64_json`, base64-decode to PNG.
- If `data[0].url`, fetch URL and save bytes.

## Prompt pattern for SaaS logo

Include:

- product name and category
- color palette
- 3–5 symbolic layers
- “no text” for icon-only mark
- “premium B2B SaaS”
- “works as app logo and favicon”
- transparent background if supported

Example symbolic layers for Organic Lead Engine:

- blue = trust/SaaS/data
- AI agent motif = autonomous workflow
- sprout/growth arrow = organic SEO compounding
- orbit/search lens = crawl + feedback loop
- neural nodes = routing/enrichment/agent system

## App integration

For Next.js/TailAdmin:

```txt
public/images/logo/<product-logo>.png
```

Replace only logo/avatar assets and text first. Do not reformat the accepted dashboard layout.

## Verification

1. Run a vision check for small-size/logo clarity.
2. Build the app.
3. Verify public HTML references the logo file.
4. Keep a simpler future variant in backlog if mark is too busy for favicon.
