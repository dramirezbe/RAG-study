# GEMINI.md — RAG-study

## Repository Overview

Research repository on **RAG (Retrieval-Augmented Generation)** for regulatory and technical documentation (FM spectrum: FCC Part 73, ANE Colombia, ITU-R BS.412/BS.450/SM.2152, ISO/IEC 17025) at Universidad Nacional de Colombia.

This is a mixed-content repository comprising LaTeX specification documents, Jupyter notebooks, study notes, agent workflows, and prompt engineering experiments.

---

## `mcp-local-rag` (Local RAG MCP Server)

The repository integrates with the **`mcp-local-rag`** Model Context Protocol (MCP) server for local, privacy-preserving hybrid (semantic + full-text keyword) search across regulatory and technical standards.

### MCP Configuration

- **Antigravity Global Config**: `~/.gemini/config/mcp_config.json`
- **OpenCode Config**: `~/.config/opencode/opencode.json`
- **Upstream Repository**: [https://github.com/shinpr/mcp-local-rag](https://github.com/shinpr/mcp-local-rag)

```json
{
  "mcpServers": {
    "local-rag": {
      "command": "npx",
      "args": ["-y", "mcp-local-rag"],
      "env": {
        "BASE_DIR": "/home/javastral/RAG-documents"
      }
    }
  }
}
```

### Document Corpus & Index State

- **`BASE_DIR` / Document Root**: `/home/javastral/RAG-documents/` (external to this git repository).
- **Live Index Metrics**: **8 documents / 7,083 chunks** (hybrid semantic + FTS search).
- **Ingested Reference Standards**:
  1. `47_CFR_Part_73.pdf` — FCC Part 73 (Radio Broadcast Services: §73.317, §73.1545, etc.)
  2. `ANE_0105_2020.pdf` — ANE Resolution 105 of 2020 (Colombia technical spectrum standards)
  3. `ANE_0406_2026.pdf` — ANE Resolution 406 of 2026 (Recent regulatory updates)
  4. `ANE_0463_2020.pdf` — ANE Resolution 463 of 2020 (FM broadcasting framework)
  5. `BS.412-9.pdf` — ITU-R Recommendation BS.412-9 (Planning standards for FM sound broadcasting)
  6. `BS.450-4.pdf` — ITU-R Recommendation BS.450-4 (Transmission standards for FM sound broadcasting)
  7. `ISO_IEC_17025_2017.pdf` — General requirements for competence of testing/calibration labs
  8. `SM.2152.pdf` — ITU-R Report SM.2152 (Definitions of Software Defined Radio and Cognitive Radio)

> [!IMPORTANT]
> The folders `RAG/Design-FMSensor/docs-RAG-FM/` and `RAG/implementation/notebooks/RAG-docs-test/` are **local reading copies** for human inspection only. Never treat them as the live RAG index or attempt to ingest from them.

### MCP Tools Reference

| Tool Name | Key Parameters | Description |
|-----------|----------------|-------------|
| `query_documents` / `local-rag_query_documents` | `query` (string, required), `limit` (int, default: 10, max: 20), `scope` (string, optional absolute path prefix) | Performs hybrid semantic retrieval + keyword reranking across indexed documents. |
| `read_chunk_neighbors` / `local-rag_read_chunk_neighbors` | `filePath` (string), `chunkIndex` (int), `count` (int, default: 2) | Reads surrounding chunks before and after a query hit for complete regulatory context. |
| `status` / `local-rag_status` | *None* | Returns the live status of the RAG index (`documentCount`, `chunkCount`, `searchMode`, `memoryUsage`). |
| `list_files` / `local-rag_list_files` | *None* | Lists all supported files in `BASE_DIR` and their ingestion status. |
| `sync_start` / `local-rag_sync_start` | `path` (optional) | Synchronizes/re-indexes files from `BASE_DIR`. |
| `sync_status` / `local-rag_sync_status` | `jobId` | Polls the progress of an asynchronous sync job. |
| `ingest_file` | `filePath` (string, absolute) | Ingests or replaces a specific file inside `BASE_DIR`. |
| `delete_file` | `filePath` (string, absolute) | Removes a file and its chunks from the index. |

### Regulatory Query Cheat Sheet

When verifying technical specs or checking compliance thresholds, run targeted queries through `query_documents`:

| Topic / Requirement | Recommended Query | Target Norm / Section |
|---------------------|-------------------|-----------------------|
| Carrier Frequency Tolerance | `carrier frequency tolerance FM broadcast 2000 Hz` | FCC §73.1545, ANE 0105 |
| Occupied Bandwidth & Emission Mask | `occupied bandwidth 99 percent FM emission mask attenuation` | FCC §73.317, ITU-R BS.450 |
| RF Protection Ratios & Desensitization | `RF protection ratios co-channel adjacent channel audio frequency` | ITU-R BS.412-9 Annex 1 |
| Multiplex (MPX) Modulation & Deviation | `maximum frequency deviation 75 kHz stereophonic pilot 19 kHz` | ITU-R BS.450-4, FCC §73.322 |
| Measurement Uncertainty & Decision Rules | `decision rule guard-band uncertainty ISO 14253 conformity assessment` | ISO/IEC 17025 §3.7 / §7.8.6, JCGM 106 |
| SDR Architecture & Reconfigurability | `Software Defined Radio digital processing flexible architecture` | ITU-R SM.2152 |

---

## Repository Map

| Path | Description | Rules / Guidelines |
| --- | --- | --- |
| `RAG/Design-FMSensor/` | LaTeX specification: SDR FM compliance monitoring (hardware-agnostic, RAG-verified). | [`RAG/Design-FMSensor/GEMINI.md`](file:///home/javastral/GIT/UNAL/RAG-study/RAG/Design-FMSensor/GEMINI.md) |
| `RAG/mcp-explore/` | Comparative analysis and architectural notes for MCP RAG servers. | [`RAG/mcp-explore/GEMINI.md`](file:///home/javastral/GIT/UNAL/RAG-study/RAG/mcp-explore/GEMINI.md) |
| `RAG/explanation/` | Theoretical RAG treatise (LaTeX) + educational notebook + normalized corpus. | [`RAG/explanation/GEMINI.md`](file:///home/javastral/GIT/UNAL/RAG-study/RAG/explanation/GEMINI.md) |
| `RAG/implementation/` | Local RAG PoC, Chroma/LanceDB implementations, architectural evaluation. | [`RAG/implementation/GEMINI.md`](file:///home/javastral/GIT/UNAL/RAG-study/RAG/implementation/GEMINI.md) |
| `prompting/PromptEngineering/` | MarkItDown conversion tutorials, slide decks, paper references. | [`prompting/PromptEngineering/GEMINI.md`](file:///home/javastral/GIT/UNAL/RAG-study/prompting/PromptEngineering/GEMINI.md) |
| `prompting/notebook-python/` | Jupyter prompt experiment notebooks, auditing skills, output comparisons. | [`prompting/notebook-python/GEMINI.md`](file:///home/javastral/GIT/UNAL/RAG-study/prompting/notebook-python/GEMINI.md) |
| `agents/` | Spec-Driven Development (SDD) methodologies, agent evolution studies, orchestrators. | English/Spanish technical essays. |
| `LLMs/benchmarks/`, `LLMs/context-window/` | SOTA LLM benchmark and context-window study notes. | Spanish study notes (do not translate). |
| `hw/` | Local hardware constraints and setup for AI/DSP experiments. | Reference notes. |
| `.agents/skills/` | Project-scoped agent skills (`jupyter-notebook-editor`, `excalidraw-diagram`, etc.). | Reusable skills. |

---

## Core Conventions

- **Language Policy**:
  - Technical artifacts, code, specs, and LaTeX documents must be in **English**.
  - Spanish is acceptable for study notes (`LLMs/`, `agents/` essays, SDD notes) — do not translate existing Spanish notes.
- **Git Hygiene**:
  - Conventional commit messages (e.g., `feat: ...`, `fix: ...`, `docs: ...`).
  - No AI attribution (no `Co-Authored-By` trailers).
  - Single root `.gitignore` manages all ignores across the repository.
  - **Never commit** inside `RAG/mcp-explore/explore-apps/clones/` or `**/copy/` (embedded upstream repos).
- **LaTeX Compilation Rules**:
  - **Always** compile using: `latexmk -g -outdir=report report/<file>.tex`
  - **Always** clean using: `latexmk -c -outdir=report report/<file>.tex`
  - Never compile without `-outdir=report` to avoid stale root PDFs and untracked build clutter.
- **Python & Virtual Environments**:
  - Area-specific venvs must be used:
    - `prompting/notebook-python/venv/`
    - `prompting/PromptEngineering/venv-markitdown/` (uv-created; manage with `uv pip`)
    - `RAG/implementation/.venv/`
  - Keep notebook outputs tidy; never embed private tokens or credentials.

---

## Project Skills

The following skills are available and should be loaded when applicable:
- `jupyter-notebook-editor`: Cell-level inspection and editing for `.ipynb` files.
- `excalidraw-diagram`: Generating Excalidraw architecture and conceptual diagrams.
- `cognitive-doc-design` / `judgment-day`: Quality auditing and adversarial review passes for technical documentation.
