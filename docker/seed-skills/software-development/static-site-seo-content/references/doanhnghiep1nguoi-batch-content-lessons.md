# Doanh Nghiệp 1 Người batch content lessons

Session lessons from manually thickening a static Vietnamese SEO site to 10 articles.

## Durable workflow lessons
- Do not trust a cron `run` request as completed work until job state shows `last_run_at`/status or repo changes confirm it. If immediate output is required, do a manual run in-session.
- Before pushing new static article HTML, verify referenced CSS/image assets exist in repo and are not ignored by `.gitignore`.
- If using a shared stylesheet path like `/assets/styles.css`, create/commit that file or inline style consistently. Missing CSS can pass content validation but produce broken live UI.
- If `.gitignore` ignores `styles.css`, use `git add -f assets/styles.css` when intentionally committing shared CSS.
- Raw Python strings can accidentally emit escaped HTML attributes such as `class=\"note\"`; read first 30–40 lines of generated HTML before build/push.
- For image generation failures caused by missing provider keys, create a simple SVG hero asset manually rather than publishing without an image when user asked for visuals.
- For user-facing reports on this project, prefer concise HTML blocks (`<section>`, `<ul>`, `<code>`) when requested.

## Verification checklist before commit
1. `read_file` first lines of new article: title/meta/canonical/style links/schema/body opening.
2. Search for `\\\"` or malformed escaped attributes in generated HTML.
3. Confirm hero image file exists if `og:image` or `<img src>` references it.
4. Run `npm run build && git diff --check`.
5. Check `git status --short --untracked-files=all`; force-add ignored intended assets.
6. Commit/push only after build passes and assets are staged.
