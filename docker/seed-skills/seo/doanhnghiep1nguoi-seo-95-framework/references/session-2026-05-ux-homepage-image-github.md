# Session notes: UX homepage, Codex images, strict audit, private agent repo

## Audit honesty correction
User challenged a false/permissive `100.0` report. Durable lesson:
- A score alone is not pass.
- Pass requires threshold met AND empty issue list.
- If user calls audit "ảo", immediately tighten validator and print average plus per-slug issue list.
- Separate article SEO/content audit from homepage UX/Lighthouse-style claims.

## UX-centric article framework additions
User provided UX 95 framework emphasizing:
- Direct `bạn` language.
- Storytelling opening in real Vietnamese situations: Facebook/Zalo jobs, personal bank transfers, client asks for invoice/contract.
- Actionable 3–4 bullet fast answer.
- Visual risk tables with 🟢/🟡/🔴 icons.
- Checkbox-style 30-minute checklist.
- Case story progression: ban đầu → vấn đề → giải pháp → lesson learned.
- 6 warning signals with description/example/action/source.
- 5 mistakes with consequence + prevention.
- FAQ ≥12 in actual search language.
- Risk/tip boxes and mobile-scannable short paragraphs.

## Homepage content hub pattern used
For `Doanh Nghiệp 1 Người`, homepage was improved from article dump to content hub:
- Hero: reader pain + primary CTA to pillar risk article.
- Situation selector: freelancer jobs, product digital, invoice request, time overload.
- Reading journey: understand model → check risk → build foundation → buy back time with AI.
- Card library: images + label + reason to read + reading time.
- Sticky/quick read path sidebar.
- 30-second risk matrix.
- ItemList JSON-LD for reading path.
- Validate: one H1, JSON-LD parses, article links present, image count present, article audit still passes.

## 9Router/Codex image generation lesson
User pointed to upstream 9Router image skill. Correction:
- Initial generator used `response_format=b64_json`; it hung/behaved poorly.
- For `cx/gpt-5.x-image`, use binary endpoint:
  `/v1/images/generations?response_format=binary`
- Body includes `model`, `prompt`, `size`, `output_format`, `background` when supported.
- Compare `content/article-image-plan.json` with `assets/` to find missing files.
- Insert only after files exist; update visible `<figure>`, `alt`, captions, `content/articles.json`, and Article JSON-LD image arrays together.

## Private AI-agent repo packaging pattern
Created private repo `ai-agent-seo-doanhnghiep1nguoi` with:
- `skills/doanhnghiep1nguoi-seo-95-framework/SKILL.md`
- `scripts/seo_95_audit.py`
- `.github/workflows/seo-95-audit.yml`
- `templates/AGENTS.doanhnghiep1nguoi.md`
- `templates/article-image-plan.example.json`
- `docs/agent-operating-playbook.md`
- `README.md`
- `.gitignore`
Before push: run secret scan for token/key markers and ensure no `.env` or secrets are committed.
