# Project-A

Generative writing pipeline powered by a single Jupyter notebook. It orchestrates outlining, drafting, editing, and exporting a long‑form book using OpenAI models, with cost controls, caching, and reproducible outputs.

The primary entry point is `notebook.ipynb`, which:
- Sets up the environment and validates API access
- Defines the book specification (`book_spec`) and pipeline knobs (`pipeline_config`)
- Generates an outline, produces chapter drafts (AuthorAgent), polishes them (EditorAgent), and assembles the manuscript
- Exports artifacts to Markdown and DOCX, with optional PDF

> Note: Folders such as `content/`, `build/`, `dist/`, `cache/`, and `logs/` are ignored by Git to keep the repo lean.

## Requirements
- Python 3.10+
- Jupyter support (VS Code Python extension or `pip install notebook`)
- OpenAI API key (`OPENAI_API_KEY`)
- Optional for PDF export:
  - Windows + Microsoft Word (for `docx2pdf`), or
  - A compatible PDF converter (LibreOffice/soffice or a working Pandoc toolchain)

## Quick Start
1) Clone the repo and create a virtual environment
```bash
python -m venv .venv
.venv\Scripts\activate  # Windows
# source .venv/bin/activate  # macOS/Linux
```

2) Install Jupyter (the notebook installs other libs as needed)
```bash
pip install notebook
```

3) Provide your OpenAI API key
- Create a `.env` file at the repo root with:
  - `OPENAI_API_KEY=your_key_here`
- Or export it in your shell before running the notebook.

4) Open and run the notebook
- Open `notebook.ipynb` in VS Code or Jupyter
- Run all cells from top to bottom
- Artifacts will be written to `build/` and `dist/`

## Notebook Overview
The notebook is organized into stages:
- Environment and imports: validates Python, installs missing packages if needed
- Configuration: sets `book_spec` (title, tone, constraints) and `pipeline_config` (models, budgets)
- Agents and routing: AuthorAgent, EditorAgent, and optional ResearchAgent
- Outline generation: produces a structured outline with chapters and guardrails
- Drafting: AuthorAgent writes chapter drafts to `content/drafts/`
- Editing: EditorAgent refines drafts and adds notes to `content/edits/`
- Assembly and QA: combines outputs into `build/book.md` and generates a `dist/manifest.json` + QA report
- Export: writes `dist/book.docx`, `dist/book.md`, and tries to produce `dist/book.pdf` if a converter is available

## Configuration & Env Vars
Key environment variables read by the notebook:
- `OPENAI_API_KEY` (required)
- `RUN_COST_CAP_USD` (default: `3.0`) — hard stop for total run cost
- `CHAPTER_COST_CAP_USD` (default: `0.25`) — per‑chapter budget
- `MODEL_ID_FAST` (default: `gpt-4o`) — primary model for generation
- `MODEL_ID_THINK` (default: `gpt-5`) — optional higher‑reasoning model

You can also tweak advanced knobs inside the notebook (e.g., chapter target words, token limits for author/editor, temperature) by editing `pipeline_config` in the Configuration cell.

## Repository Structure
- `notebook.ipynb`: End‑to‑end pipeline (run this)
- `content/`: Working content (outline, research, drafts, edits, style)
- `build/`: Intermediate assembled manuscript (e.g., `build/book.md`)
- `dist/`: Final exports (`book.docx`, `book.md`, optional `book.pdf`, `manifest.json`, `qa_report.json`)
- `cache/`: Cached intermediate results to save tokens/cost
- `logs/`: Detailed logs and agent call traces for debugging
- `references/`: Any source material you want to keep alongside the project

These directories are listed in `.gitignore` and are safe to delete if you want a clean rebuild (the notebook will recreate them).

## Troubleshooting
- “OPENAI_API_KEY not set”: Ensure the key is available in your environment or `.env`.
- PDF export skipped/failed: Requires Word on Windows (docx2pdf) or a configured alternative. DOCX/MD exports should still be present.
- Budget exceeded: Increase `RUN_COST_CAP_USD` / `CHAPTER_COST_CAP_USD` in env or `pipeline_config`.
- Re‑running from scratch: Stop the kernel, delete `build/`, `dist/`, `cache/`, and re‑run the notebook.

## Notes
- Model usage may incur costs; defaults aim to be conservative. Monitor outputs in `dist/manifest.json` and `logs/`.
- The default `book_spec` is a techno‑thriller scaffold you can freely customize.
