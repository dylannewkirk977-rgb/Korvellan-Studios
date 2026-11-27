# Working agreement

Korvellan Studios builds AI-powered services and business automation. This repo holds one folder per service.

## Ground rules
- Work in small steps; explain important decisions briefly.
- Prefer simple, sellable solutions over clever complexity.
- **Ask before:** installing software, major architecture changes, or destructive commands (deleting files, rewriting git history, etc.).
- **Never use the D: or F: drives.** Don't read, write, or suggest D:/F: paths in code, config, or docs. Everything lives on C: (project: `C:\Users\dylan\Downloads\Korvellan-Studios`).
- Update [docs/PROGRESS.md](docs/PROGRESS.md) when a step is finished.

## Conventions
- Python: managed with `uv` (`uv venv`, `uv pip install`, `uv run`). `python` is not on PATH.
- Node: npm, per-project `package.json`.
- Local LLMs: Ollama at `http://localhost:11434` (OpenAI-compatible at `/v1`). Keep model names and endpoints in config, never hard-coded, so models can move between GPUs.
- Large generated media and model weights stay out of git (see `.gitignore`).
- Platform: Windows 11, PowerShell.
