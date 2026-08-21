# GEMINI.md — RAG/explanation

## Project Overview

Theoretical foundation and educational treatises on Retrieval-Augmented Generation (RAG) applied to technical and regulatory domains (spectrum regulation, standards, and engineering specs).

---

## Structure & Deliverables

- `report/` — Theoretical treatise in LaTeX on RAG architectures, chunking strategies, embeddings, and hybrid retrieval.
- `notebook/` — Educational Jupyter notebook (`main.ipynb`) demonstrating the complete RAG lifecycle.
- `corpus/` — Normalized technical corpus for RAG demonstrations.
- `prompts/` — Educational prompt engineering examples for retrieval grounding.

---

## Important Gotchas

- **Notebook Corpus Fallback**: `notebook/main.ipynb` has a fallback parameter (`force_embedded=True`) allowing it to run standalone even if an external corpus path is not mounted.
- **`mcp-local-rag` Reference**: For live document retrieval during study or notebook execution, connect to the `local-rag` MCP server pointing to `BASE_DIR=/home/javastral/RAG-documents`.
- **LaTeX Compilation**: Compile report LaTeX files using `latexmk -g -outdir=report report/<file>.tex` and clean with `latexmk -c -outdir=report`.
