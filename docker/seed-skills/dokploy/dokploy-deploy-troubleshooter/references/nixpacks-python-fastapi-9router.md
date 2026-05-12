# Nixpacks Python FastAPI + 9Router app pattern

Use when deploying a small Python/FastAPI web app to Dokploy without local Docker.

## Minimal project files

`requirements.txt`:

```txt
fastapi
uvicorn[standard]
httpx
python-multipart
pypdf
python-dotenv
```

`nixpacks.toml`:

```toml
[phases.setup]
nixPkgs = ["python311", "poppler_utils"]

[phases.install]
cmds = ["python -m pip install --upgrade pip", "python -m pip install -r requirements.txt"]

[start]
cmd = "uvicorn app.main:app --host 0.0.0.0 --port ${PORT:-8000}"
```

Add optional build phase when generated knowledge/index files should exist at deploy time:

```toml
[phases.build]
cmds = ["python scripts/extract_pdfs.py || true", "python scripts/build_knowledge.py || true", "python scripts/populate_second_brain.py || true"]
```

`Procfile` optional:

```Procfile
web: uvicorn app.main:app --host 0.0.0.0 --port ${PORT:-8000}
```

## 9Router env pattern

Set in Dokploy app env or secret-file path, never commit real key:

```text
ROUTER_BASE_URL=https://9router.phamphunguyenhung.com
ROUTER_API_KEY=<secret>
ROUTER_MODEL=codex-gpt-5.3
MAX_TEXT_CHARS=50000
```

Default to the model the user requests. In recent aptitude-solver work, user explicitly wanted `codex-gpt-5.3`.

In app code, accept common aliases and secret-file mounts for compatibility:

```python
from pathlib import Path
from typing import Optional

from dotenv import load_dotenv

load_dotenv(ROOT / ".env")


def read_secret(name: str) -> Optional[str]:
    value = os.getenv(name)
    if value:
        return value.strip()
    file_value = os.getenv(f"{name}_FILE")
    if file_value and Path(file_value).exists():
        return Path(file_value).read_text(encoding="utf-8").strip()
    secret_path = Path("/run/secrets") / name
    if secret_path.exists():
        return secret_path.read_text(encoding="utf-8").strip()
    return None


ROUTER_API_KEY = read_secret("ROUTER_API_KEY") or read_secret("NINEROUTER_API_KEY") or read_secret("OPENAI_API_KEY")
ROUTER_BASE_URL = os.getenv("ROUTER_BASE_URL", "https://9router.phamphunguyenhung.com").rstrip("/")
```

Call OpenAI-compatible chat at:

```text
{ROUTER_BASE_URL}/v1/chat/completions
```

## Runtime checks

- App must bind `0.0.0.0`, not `127.0.0.1`.
- Use `${PORT:-8000}` because Dokploy/Nixpacks may set `PORT`.
- Add `/health` route returning JSON with non-secret router/model info.
- Domain should map to app port/PORT; public check should hit `/health` and `/`.

## PDF/document apps

For text extraction from PDFs, `pypdf` is enough for text PDFs. Add `poppler_utils` to Nixpacks only if using `pdftotext`, `pdftoppm`, or image conversion. For scanned PDFs or visual reasoning, upload page images/screenshots and send to a vision-capable chat model instead of relying on `pypdf`.

For knowledge-base style document apps, COG-second-brain can be used as a markdown vault:

1. Clone or vendor `COG-second-brain` under the project (or point to an existing vault).
2. Extract PDFs/text into `data/extracted`.
3. Write one booklet per source under `second-brain/05-knowledge/booklets/<topic>/`.
4. Write consolidated solver/framework notes under `second-brain/05-knowledge/consolidated/`.
5. Write reusable pattern inventories under `second-brain/05-knowledge/patterns/`.
6. At runtime, read the consolidated framework + pattern library into the system prompt with a sane character cap.

## User-facing solver prompt pattern

For Vietnamese aptitude solvers, explicitly require Vietnamese explanations even when source material is English:

```text
Bắt buộc: toàn bộ giải thích phải bằng tiếng Việt, kể cả khi đề tiếng Anh.
```

Good output:

```text
Đáp án: <chữ cái/kết quả>
Giải thích: <1-5 câu tiếng Việt>
```
