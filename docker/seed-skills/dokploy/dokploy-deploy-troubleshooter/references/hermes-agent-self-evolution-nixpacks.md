# Hermes Agent Self-Evolution on Dokploy via Nixpacks

Session-derived deploy notes for `https://github.com/NousResearch/hermes-agent-self-evolution`.

## Repo shape
- Python package, not a web server by default.
- `pyproject.toml` requires Python `>=3.10` and dependencies: `dspy`, `openai`, `pyyaml`, `click`, `rich`; dev extra adds `pytest`, `pytest-asyncio`.
- README quick start uses:
  ```bash
  pip install -e ".[dev]"
  export HERMES_AGENT_REPO=~/.hermes/hermes-agent
  python -m evolution.skills.evolve_skill --skill github-code-review --iterations 10 --eval-source synthetic
  ```

## Nixpacks settings
Use Nixpacks, not Docker, unless user provides custom server/container intent.

Install/build command:
```bash
pip install -e ".[dev]"
```

No stable long-running start command exists in upstream repo. Treat as job/worker unless user supplies a wrapper service. Candidate manual command:
```bash
python -m evolution.skills.evolve_skill --help
```

For scheduled runs, ask user for target skill, iteration count, eval source, and `HERMES_AGENT_REPO` path/volume.

## Required runtime env / secrets
- `HERMES_AGENT_REPO` must point at Hermes Agent checkout inside container or mounted volume.
- LLM provider key(s) required by DSPy/OpenAI-compatible config (for example `OPENAI_API_KEY`, `ANTHROPIC_API_KEY`, or user gateway env, depending on chosen model setup).
- Do not print secret values.

## User local convention
User indicated Dokploy config/secrets live in `user.env` and helper repo is `auto-dokploy`. If files are not in cwd, ask for exact paths instead of guessing.
