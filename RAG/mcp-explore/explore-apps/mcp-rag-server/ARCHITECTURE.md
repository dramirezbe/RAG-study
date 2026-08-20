# mcp-rag-server — Architecture

Source: https://github.com/Daniel-Barta/mcp-rag-server (v0.4.x). Explored from the cloned repository at this path.

## What it is

A lightweight RAG MCP server (TypeScript, Node >= 20) that indexes a **repository directory** (source code + docs) and exposes semantic search over the standard Model Context Protocol. Works with any MCP client: GitHub Copilot Agent mode (VS / VS Code), MCP Inspector, or custom tooling. Two transports: **stdio** (default) or **Streamable HTTP**.

## RAG approach used and why

Deliberately **minimal and simple**: fixed-size overlapping chunks + embeddings + an in-memory vector array scanned linearly with cosine similarity. The stated design goal is predictability with minimal dependencies for medium repos, no external database, and an easy extension path. The README explicitly lists what is *not* implemented yet: hybrid BM25 + embedding search, ANN acceleration (HNSW/IVF), semantic-boundary-aware chunking, batched local embedding.

Why this shape: for code-oriented corpora of medium size, a full vector DB and reranking pipeline are unnecessary complexity; embedding everything upfront and scanning in memory keeps queries deterministic and dependency-free.

### Pipeline

```
fast-glob discovery (ALLOWED_EXT, EXCLUDED_FOLDERS)
  → read file content (PDFs extracted via pdf-parse, cached in pdf-text-cache.json)
  → fixed-size overlapping chunks (splitChunks, 2400 chars / 400 overlap)
  → embeddings (local @huggingface/transformers OR OpenAI-compatible API)
  → in-memory Doc[] (path, chunk index, text, vector, fileSize, lineCount)
  → optional JSON persistence (INDEX_STORE_PATH) for warm start / incremental rebuild
  → search: linear cosine scan over all chunks, top-k sort
```

## Tools used in the RAG

| Stage | Tool |
|---|---|
| MCP server | `@modelcontextprotocol/sdk` (stdio + Streamable HTTP) |
| HTTP transport | `express` v5 (health + instructions endpoints, DNS rebinding protection) |
| Embeddings | `@huggingface/transformers` v4 (local) or any OpenAI-compatible `/embeddings` API |
| File discovery | `fast-glob` |
| PDF extraction | `pdf-parse` (text cached in a unified `pdf-text-cache.json` next to the index store) |
| Config | `dotenv` |
| Tooling | tsup (build), ESLint + Prettier, `tsc --noEmit` typecheck |

## Chunking — fixed-size with overlap

`Indexer.splitChunks(text, size, overlap)`: a single-pass character slicing loop (O(n)). Defaults: **2400 chars ≈ 800 tokens**, **400 chars overlap ≈ 120 tokens (~15%)**.

- Safety caps: `CHUNK_SIZE` clamped to 8000, `CHUNK_OVERLAP` to 4000; if overlap >= size it is auto-reduced to ~15% (logged).
- No semantic boundaries, no code-block or token awareness — acknowledged in the source as naïve; the README's chunk-size guidance (smaller for pinpoint code retrieval, larger for narrative docs) is the tuning tool.

## Embeddings

- Default model (local): `jinaai/jina-embeddings-v2-base-code`, loaded **quantized (q8)**, mean pooling + L2 normalization — chosen for mixed natural-language + source-code corpora.
- Alternatives: `Xenova/bge-base-en-v1.5` (English docs), `Xenova/bge-small-en-v1.5` (fast/light), or remote `text-embedding-3-small`/`text-embedding-3-large` via `EMBEDDING_PROVIDER=openai`.
- Local inference is **sequential (one chunk at a time)**; the OpenAI-compatible provider batches **200 chunks per request** (`EMBEDDING_API_BATCH_SIZE`).
- `getModelIdentity()` = `provider:model` — used to invalidate persisted indexes when the embedding setup changes.

## Vector database

**There is no vector database.** Docs and their `Float32Array` embeddings live in a plain in-memory array; `rag_query` embeds the query and computes cosine similarity against every chunk (linear scan, O(n)), sorting descending (score 0–1, rounded to 4 decimals). ANN (HNSW/IVF) is listed as a future enhancement.

Optional persistence (`INDEX_STORE_PATH`) writes the index as multiple JSON files (`.mcp-index.part0000.json` …) plus a manifest (`.mcp-index.manifest.json`) tracking version, chunk parameters, model identity, and the file list. On startup the manifest is validated and the index loaded, then an **incremental rebuild** runs:

- removed files → their chunks discarded;
- new or **file-size-changed** files → re-chunked and re-embedded;
- unchanged files → embeddings reused (warm start).

Known limitation: change detection is size-only, so edits that keep the same byte size are missed (no hashing/mtime yet).

## MCP surface

- `rag_query` — semantic search: `{ query, top_k? }` (default 5, max 50) → scored snippets with `path`, `score`, `snippet`, `totalLines`, `fileSize`.
- `read_file` — safe file read with optional 1-based `startLine`/`endLine` range; PDFs served from the text cache; absolute paths rejected.
- `list_files` — directory listing with `recursive`, `maxDepth`, `includeExtensions`, `limit` (default 500, cap 5000).

Transports: stdio by default; `MCP_TRANSPORT=http` runs Streamable HTTP on `127.0.0.1:3000/mcp` with a `/health` readiness endpoint (ready flips only when all chunks are embedded) and `/instructions` (serves `docs/copilot-instructions.md`). Host allow-list / DNS rebinding protection is on by default.

Security: `Indexer.ensureWithinRoot` rejects any path resolving outside `REPO_ROOT` (directory-traversal guard); `read_file` may still read any file inside the root regardless of `ALLOWED_EXT`.

## Testing

**No test suite exists.** The repo ships only static checks: `npm run typecheck` (tsc --noEmit), `npm run lint` (ESLint), `npm run format:check` (Prettier). There is no unit, integration, or E2E coverage — a clear gap versus mcp-local-rag, which has a full Vitest suite. Verification of behavior relies on the MCP Inspector and manual runs.