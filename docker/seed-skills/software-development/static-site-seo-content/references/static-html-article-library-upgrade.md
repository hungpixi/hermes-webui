# Static HTML article library upgrade notes

Use when user asks to nâng cấp chất lượng nội dung đồng đều across `content/articles` and homepage.

## Pattern

1. Inventory both rendered article files and index metadata.
   - Count `content/articles/*.html` and parse `content/articles.json`.
   - Diff slugs: rendered-only pages may exist but be absent from JSON index, so homepage won't list them.
2. Classify pages before editing.
   - Keep strong long-form/pillar pages intact unless specific issue found.
   - Normalize thin/uneven pages with shared structure: fast summary, audience, key point, context, self-check table, steps, pitfalls, checklist, FAQ, sources, related links.
3. Update metadata and homepage together.
   - Add missing rendered pages to `articles.json` with `canonicalPath`, `relatedSlugs`, `sources`, `sourceCheckedAt`, caveat, schema hints.
   - Regenerate or patch homepage article cards from JSON so counts and card groups match real published inventory.
4. Preserve SEO/trust basics.
   - Keep canonical URL, meta description, OG title/description, JSON-LD, caveat for legal/tax/YMYL content, source links, and internal links.
5. Validate.
   - Parse `articles.json` with Node/Python.
   - Run `node --check server.js` or project equivalent.
   - Check `git diff --stat` for suspicious mass deletions; big byte drops may mean useful long content was overwritten.

## Pitfalls

- Do not assume `articles.json` contains every published HTML file. Cross-check filesystem.
- Do not make all pages shorter in name of consistency. Prefer raising thin pages up to benchmark pages, not flattening strong pages down.
- If using temporary scripts, delete them before final status.
- On Windows projects invoked through bash, use `rm` not `del` unless running in cmd/PowerShell.
