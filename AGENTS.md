# AGENTS.md — RAG-study

## Repo overview

Research repository on RAG for regulatory/technical documentation (FM spectrum: FCC Part 73, ANE Colombia, ITU-R), at Universidad Nacional de Colombia. Mixed-content repo: LaTeX documents, Jupyter notebooks, study notes, agent skill files. Each area has its own `AGENTS.md` with local rules — read the one for the subtree you touch.

## Repository map

| Path | What it is | Own AGENTS.md |
| --- | --- | --- |
| `RAG/explanation/` | RAG theory treatise (LaTeX), teaching notebook, normalized regulatory corpus (`corpus/`) | — |
| `RAG/implementation/` | MCP-based local RAG PoC: notebook, Chroma index (gitignored), QA artifacts, architecture docs (`context/`) | — |
| `RAG/Design-FMSensor/` | LaTeX spec: SDR FM compliance monitoring, RAG-informed refactor (LAP1 done, LAP2 pending) | ✅ `AGENTS.md` |
| `RAG/mcp-explore/` | MCP RAG server comparison (docs only; upstream clones gitignored) | ✅ `AGENTS.md` |
| `agents/` | SDD methodology, agent evolution essay, DesignAgentOrchestrator SOTA, SDD tutorial | — |
| `prompting/PromptEngineering/` | MarkItDown tutorial, slide deck, paper references | ✅ `AGENTS.md` |
| `prompting/notebook-python/` | Notebook prompt experiments (multi-model outputs, comparisons) | ✅ `AGENTS.md` |
| `LLMs/benchmarks/`, `LLMs/context-window/` | SOTA study notes (Spanish) | — |
| `hw/` | Local machine hardware constraints for AI experiments | — |
| `.agents/skills/` | Project-scoped skills (excalidraw, jupyter-notebook-editor, etc.) | — |

## Conventions

- **Language**: technical artifacts and docs in English. Spanish is acceptable for study notes (`LLMs/`, `agents/` essays, SDD notes) — do not translate existing Spanish notes.
- **Commits**: conventional commits, no AI attribution (no `Co-Authored-By`).
- **LaTeX**: compile with `latexmk -g -outdir=report report/<file>.tex` and clean with `latexmk -c -outdir=report` — always pass `-outdir=report`, never compile from the project root (stale PDF + untracked junk). See `RAG/Design-FMSensor/AGENTS.md`.
- **Notebooks**: run with the area venv (`prompting/notebook-python/venv/`, `RAG/implementation/.venv/`); keep outputs tidy; no secrets. See `prompting/notebook-python/AGENTS.md`.
- **Never commit** into `RAG/mcp-explore/explore-apps/clones/` (embedded upstream repos, gitignored).

## Generated artifacts (gitignored — do not commit)

- `**/lancedb/`, `models/`, `RAG/implementation/notebooks/chroma-rag-index/` — vector indexes and model files, regenerable by running the notebooks.
- `.atl/` — auto-generated skill-registry caches (a tracked copy in `agents/TutorialSDDAgents/.atl/` was removed 2026-08-20).
- `RAG/mcp-explore/explore-apps/clones/`, `**/copy/` — embedded upstream repos.
- `*.Zone.Identifier` — Windows junk (a tracked one was removed 2026-08-20; keep it out).
- Single root `.gitignore` only — no per-area ignore files (consolidated 2026-08-20).

## Gotchas

- **RAG index is external**: the FM regulatory corpus index lives at `/home/javastral/RAG-documents/` (local-rag server), not in this repo. `RAG/Design-FMSensor/docs-RAG-FM/` and `RAG/implementation/notebooks/RAG-docs-test/` are reading/ingestion copies only. Verify live state with `local-rag_status` (expected: 8 documents / 7083 chunks).
- **Stale corpus path**: `RAG/explanation/notebook/main.ipynb` references `RAG-research/corpus/...` which does not exist here — the notebook has an embedded/standalone corpus fallback (`force_embedded=True`), so it runs without the external path.
- **`prompting/PromptEngineering/venv-markitdown`**: uv-created, no pip, shebangs were sed-repaired after a rename. Install with `uv pip install --python venv-markitdown/bin/python ...`, never `pip`.
- **Two LaTeX projects share corpus PDFs**: `RAG/Design-FMSensor/docs-RAG-FM/` and `RAG/implementation/notebooks/RAG-docs-test/` hold copies of the same ANE documents; keep them in sync if the corpus changes.
- **Naming history**: `LLMs/benchmarks/` (was `benckmarks`) and `prompting/PromptEngineering/` (was `PromptEngineerig`) were renamed 2026-08-20; no code references them by path.

## Skills to load (project-scoped)

- `jupyter-notebook-editor` — precise cell-level editing of `.ipynb` files.
- `excalidraw-diagram` — diagrams for architecture/docs work.
- `cognitive-doc-design` / `judgment-day` — document quality passes (per `RAG/Design-FMSensor/AGENTS.md`).