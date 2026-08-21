# GEMINI.md — RAG/mcp-explore

## Project Overview

Exploration and comparative evaluation area for RAG Model Context Protocol (MCP) servers.

The primary artifact in this directory is the comparative study across different local RAG MCP server architectures (including `mcp-local-rag` and `mcp-rag-server`).

---

## Directory Structure & What Lives Here

- `explore-apps/clones/` — Cloned upstream repositories (each containing its own `.git/`).
  > [!WARNING]
  > Never commit or stage changes inside `explore-apps/clones/`. The whole directory is gitignored.
- `explore-apps/mcp-local-rag/` — Analysis artifacts for `shinpr/mcp-local-rag`:
  - `LINK.md` — Upstream GitHub repository reference and cloning commands.
  - `ARCHITECTURE.md` — Ingestion pipeline, semantic chunking, vector database, and local embedding models.
  - `HOW_TO_USE.md` — Environment parameters (`BASE_DIR`, `DB_PATH`, `EMBEDDING_MODEL`) and client configuration.
- `explore-apps/mcp-rag-server/` — Analysis artifacts for `mcp-rag-server`.
- `explore-apps/COMPARE.md` — Side-by-side architectural and functional comparison.

---

## `mcp-local-rag` Highlights

- **Repository**: [https://github.com/shinpr/mcp-local-rag](https://github.com/shinpr/mcp-local-rag)
- **Primary Transport**: `stdio` via `npx -y mcp-local-rag`
- **Key Environment Variables**:
  - `BASE_DIR`: Absolute path serving as document root and security boundary (e.g. `/home/javastral/RAG-documents`).
  - `DB_PATH`: Optional custom database storage path (defaults to internal cache directory).
  - `EMBEDDING_MODEL`: Hugging Face embedding model identifier.
- **Search Capabilities**: Hybrid semantic search + BM25/FTS keyword reranking.

---

## Exploration Rules

1. **Artifact Placement**: All documentation must live outside the `explore-apps/clones/` directory (e.g. at `explore-apps/<name>/`).
2. **Code Verification**: Documentation and architecture assessments must derive from reading actual source code (manifest, chunker, embedder, vector store, server entry), not assumptions from README marketing text.
3. **Language**: All artifacts and comparison documents must be written in **English**.
