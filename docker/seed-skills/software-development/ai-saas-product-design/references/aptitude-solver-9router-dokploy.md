# Aptitude solver via 9Router + Dokploy

Use when user wants a quick web app that solves aptitude/case/logic questions with a private 9Router endpoint and simple HTML UI.

## Reference implementation shape

- Backend: FastAPI.
- Frontend: static `index.html`, `styles.css`, `app.js` mounted by FastAPI.
- AI gateway: OpenAI-compatible chat completions at `https://9router.phamphunguyenhung.com/v1/chat/completions`.
- Env names accepted: `ROUTER_API_KEY`, `NINEROUTER_API_KEY`, or `OPENAI_API_KEY`.
- Default base URL: `ROUTER_BASE_URL=https://9router.phamphunguyenhung.com`.
- Default model: `ROUTER_MODEL=codex-gpt-5.3` when user asks for their GPT-5.3/Codex route; otherwise pick a vision-capable OpenAI-compatible model for image solving and keep env override.
- Deployment: prefer Nixpacks/Procfile on Dokploy when local Docker is unavailable; Dockerfile/compose only when user wants Docker. Service port `8000`.
- Domain mapping example: `aptitude.phamphunguyenhung.com -> service port 8000`.
- Health check: `/health`.

## Endpoint pattern

```txt
GET  /health
POST /api/solve       JSON: { question, mode }
POST /api/solve-file  multipart: question, mode, files[]
```

## Prompt pattern

System prompt should force short Vietnamese answer format:

```txt
Bạn là bộ giải đề aptitude/case/logic test bằng tiếng Việt.
Nhiệm vụ: trả đáp án nhanh và giải thích mẫu ngắn gọn.
Ưu tiên format:
Đáp án: <chữ cái/kết quả>
Giải thích: <1-5 câu>
Nếu nhiều câu, dùng:
Câu 1: <đáp án> — <giải thích ngắn>
Câu 2: ...
Với logic hình: tìm quy luật vị trí, chiều quay, số lượng, shading, mirror/flip, cạnh hình, điều kiện mũi tên.
Với numerical: tính rõ công thức chính, kiểm tra đơn vị.
Nếu thiếu dữ kiện hoặc ảnh không đọc được, nói rõ thiếu gì. Không bịa.
```

## File handling

- PDFs: extract text with `pypdf` for text-based PDFs; limit chars to avoid oversized requests.
- Scanned PDFs/logic figures: prefer uploaded images/screenshots and send as `image_url` data URLs to vision-capable model.
- Visual aptitude/IQ tests are image-first: text rules alone are insufficient for both solving and study. Render question/solution PDF pages to PNG and show images beside extracted rules.
- Text/Markdown: read UTF-8 with ignore errors.

## Dokploy env block

```txt
ROUTER_BASE_URL=https://9router.phamphunguyenhung.com
ROUTER_API_KEY=<secret env key>
ROUTER_MODEL=codex-gpt-5.3
MAX_TEXT_CHARS=50000
```

## Pitfalls

- Do not commit secrets; use Dokploy env/secrets.
- Do not include large raw PDF books in Docker context; add `.dockerignore` for `*.pdf`, extracted data, outputs.
- If building with vision, choose a vision-capable `ROUTER_MODEL`; text-only models cannot read uploaded images.
- For local verification, compile Python and test `/health`; lack of local Docker/pip is environment setup, not app logic failure.
- If user says the API key is already in env/secrets, do not keep asking for the key. Implement secret lookup (`ROUTER_API_KEY`, `NINEROUTER_API_KEY`, `OPENAI_API_KEY`, optional `*_FILE`, `/run/secrets/<name>`) and test with a dummy local key only when necessary.
- If user says they do not have Docker, switch to Nixpacks/Procfile immediately: `nixpacks.toml` with Python setup/install/start phases and `uvicorn app.main:app --host 0.0.0.0 --port ${PORT:-8000}`.
- If user requests a specific model (e.g. `codex-gpt-5.3`), make it default via `ROUTER_MODEL`, update docs/examples, and keep env override.

## Knowledge-base / study page extension

For solver apps built from PDFs or docs, add a study/knowledge page when user wants to review rules, not only solve questions. Reference pattern:

- Extract PDFs into `data/extracted/*.txt`.
- Build normalized knowledge JSON under `data/knowledge/`, with Vietnamese explanations if target UX is Vietnamese.
- Clean extraction aggressively: strip page headers/footers/copyright boilerplate, `--- Page`, next-question prompts, and duplicated source noise before presenting rule cards or feeding prompts.
- For visual aptitude/IQ/inductive reasoning PDFs, render pages to images (e.g. `pdftoppm -png -r 140`) into `static/pdf-pages/<pdf-slug>/page-N.png`; map question `qid` to page `qid+1` when first page is instructions. Show question image and solution image side-by-side on study cards. If local lacks Poppler, add `poppler_utils` to Nixpacks and render during deploy/build.
- Optionally clone/use a markdown second-brain vault (e.g. COG) and write:
  - `05-knowledge/booklets/<domain>/` for one markdown per source doc.
  - `05-knowledge/consolidated/<domain>-framework.md` for taxonomy, output rules, solver workflow, examples.
  - `05-knowledge/patterns/<domain>-pattern-library.md` for grouped rule types.
- Add `/knowledge` page and APIs like `GET /api/knowledge`, `GET /api/knowledge/doc/{slug}`, `GET /api/knowledge/framework`.
- UI should include menu link, stats, searchable/filterable rule cards, framework/method section, document cards, viewer, copy button, and for visual tests image previews next to rules. Avoid text-only study pages for image-based tests.
- For aptitude/IQ learning UX, prefer a Quizlet-like flashcard mode above raw rule lists: front side shows question image and prompt to infer rule/answer; back side shows solution image, official-style explanation, and why the chosen option fits. User explicitly wants to see the question, answer explanation, and rationale, not only extracted rules.
- Generate per-question explanation fields, not only taxonomy labels: `solution_steps_vi` should follow `quan sát -> rule 1 -> rule 2 -> suy ra đáp án -> vì sao loại/chọn`. If official solution text exists, preserve cleaned original rule text under details and present Vietnamese explanation prominently.
- Use a Vietnamese-friendly font such as Noto Sans, light readable result blocks, and generous line-height; avoid dark monospace `pre` blocks for Vietnamese explanations unless code/log output.
- Feed condensed framework/pattern markdown into runtime system prompt, but cap size.

## UX notes

- User prefers simple bright, readable HTML for this class of quick solver tool.
- Provide fast mode by default, with optional “detail” mode.
- Result block should be easy to copy.
