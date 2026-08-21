# GEMINI.md — RAG/implementation

## Project Overview

Local RAG implementation and Proof of Concept (PoC) using Model Context Protocol (MCP) servers, vector databases (LanceDB, Chroma), and embedding pipelines.

---

## Directory Map

- `context/` — Architectural decision records, SOTA 2026 evaluation guide (`CONTEXT.md`), and curated resources.
- `notebooks/` — Implementation prototypes and benchmarking notebooks.
- `report/` — Implementation notes and evaluation documents.
- `lancedb/` / `chroma-rag-index/` — Generated vector database indexes (gitignored).

---

## `mcp-local-rag` Integration & Guidelines

- The primary reference MCP server is **`mcp-local-rag`** ([shinpr/mcp-local-rag](https://github.com/shinpr/mcp-local-rag)).
- **Base Directory**: `/home/javastral/RAG-documents` (contains 8 regulatory standards / 7,083 chunks).
- **Environment**: Configured via `~/.gemini/config/mcp_config.json` and `~/.config/opencode/opencode.json`.
- **Query Tools**: Use `local-rag_query_documents` / `query_documents` for hybrid retrieval, `local-rag_read_chunk_neighbors` for adjacent chunk context, and `local-rag_status` for index verification.

---

## Environment & Python Rules

- Use the dedicated virtual environment at `.venv/`.
- Keep vector database indexes out of version control (handled by root `.gitignore`).
