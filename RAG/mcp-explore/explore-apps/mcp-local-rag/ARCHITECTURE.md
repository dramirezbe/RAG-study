# mcp-local-rag — Architecture

Source: https://github.com/shinpr/mcp-local-rag (v0.17.x, MIT). Explored from the cloned repository at this path.

## What it is

An MCP server + CLI (TypeScript, Node >= 22) that indexes private documents (PDF, DOCX, TXT, Markdown, HTML) and serves **fully local semantic search** over the standard MCP stdio protocol. No API key, Docker, Python, or external database is required: parsing, embeddings, storage, and search all run in-process on the machine.

## RAG approach used and why

Two design decisions define this RAG:

1. **Local-only inference** — embeddings are generated on-device via Transformers.js (ONNX runtime). The stated motivation: some document sets cannot be sent to a hosted embedding service because of confidentiality or organizational policy, and keeping the index local removes per-query API cost. After the initial model download (~90 MB), ingestion and search work offline.

2. **Hybrid search (semantic + keyword)** — vector similarity alone misses exact technical identifiers (API names, class names, error codes). A BM25 full-text index is used to **boost** exact-term matches on top of semantic retrieval, so queries match both intent and literal terms.

### Ingestion pipeline

```
parse → sentence split → semantic chunk (Max–Min) → embed (batch) → LanceDB table + FTS index
```

### Search pipeline

```
embed query → vector search (dot) → [scope prefix filter] → [maxDistance filter]
           → [relevance-group filter] → keyword boost (BM25) → [top-N-files filter] → top-k
```

## Tools used in the RAG

| Stage | Tool |
|---|---|
| MCP server | `@modelcontextprotocol/sdk` (stdio transport) |
| Embeddings | `@huggingface/transformers` v4 (feature-extraction pipeline, ONNX runtime) |
| Vector DB + FTS | `@lancedb/lancedb` (embedded, no server) |
| PDF parsing | `mupdf` |
| DOCX parsing | `mammoth` + `jszip` |
| HTML → Markdown | `@mozilla/readability` + `turndown` + `jsdom` |
| Test/lint tooling | Vitest, Biome, knip, dpdm (GitHub Actions CI) |

## Chunking — semantic (Max–Min algorithm)

This is the distinctive part. Instead of fixed-size slicing, chunks are built at **topic boundaries**:

1. Text is split into sentences (`sentence-splitter.ts`).
2. Every sentence is embedded with the same model.
3. A greedy grouping (implementing the *Max–Min semantic chunking* algorithm, Springer 2025) decides per sentence: add it to the current chunk if its **max similarity to any chunk member** exceeds a **dynamic threshold** built from the chunk's **min pairwise similarity**: `threshold = max(c · minSim · sigmoid(|C|), hardThreshold)`, with defaults `hardThreshold = 0.6`, `initConst = 1.5`, `c = 0.9`.

Performance safeguards:
- `WINDOW_SIZE = 5`: min-similarity only compares the last 5 sentences → O(1) instead of O(k²).
- `MAX_SENTENCES = 15`: forced split cap on homogeneous documents.
- `minChunkLength = 50` chars (configurable via `CHUNK_MIN_LENGTH`) plus a language-agnostic garbage filter (decoration lines, >80% single repeated char).
- Markdown **code blocks are preserved as atomic ranges** — never split mid-block.

Why: documents chunked at topic boundaries retrieve more coherent context than fixed-size chunks that cut through content mid-thought.

## Embeddings

- Default model: `Xenova/all-MiniLM-L6-v2` (~90 MB, 384-dim), configurable via `MODEL_NAME` (e.g. `Xenova/bge-small-en-v1.5`, `Xenova/bge-base-en-v1.5`).
- Generation: `pooling: 'mean'` + L2 normalization; true batched inference (batch size 8) with padding-amplification outlier deferral (long texts are embedded alone to avoid wasted padding).
- Lazy initialization on first use; `RAG_DEVICE` (cpu/webgpu) and `RAG_DTYPE` (default fp32) are passed through to ONNX runtime. Changing model/device/dtype invalidates existing vectors — use a new `DB_PATH` or re-ingest.

## Vector database

- **LanceDB embedded**: a local folder (`./lancedb/` by default), no server process, no external DB.
- Table schema: `filePath`, `chunkIndex`, `text`, `metadata`, `vector`, `fileTitle`, `contentHash`, `timestamp`.
- Distance: **dot product** over L2-normalized vectors (equivalent to cosine).
- **FTS index** (`fts_index_v2`) with an **ngram tokenizer** (min 2, max 3, prefixOnly false, stem false) for multilingual/CJK support.
- `contentHash` per file enables incremental `sync_start` (byte-identical files are skipped; deleted files pruned); schema migrations and explicit `optimize()` (compaction + FTS refresh) are handled.

## Search — hybrid reranking details

1. Vector search (dot), fetching `limit × 2` candidates; optional `scope` path-prefix prefilter and `maxDistance` threshold pushed down to the query.
2. Optional relevance-group filter: detects significant score gaps (mean + 1.5σ) — `similar` keeps 1 group, `related` keeps up to 2.
3. Keyword boost: BM25 scores normalized 0–1, then applied multiplicatively `score / (1 + kw · weight)` with default weight 0.6 (`RAG_HYBRID_WEIGHT`) — exact-term matches improve ranking without dominating semantics.
4. Optional top-N-files filter (`RAG_MAX_FILES`), then top-k.

## MCP surface (stdio)

`sync_start`, `sync_status`, `ingest_file`, `ingest_data` (text/Markdown/HTML), `query_documents`, `read_chunk_neighbors`, `list_files`, `delete_file`, `status`. Optional PDF **visual mode** (opt-in) captions figure-heavy pages via local VLM profiles (`fast` ~250 MB, `quality` ~2.9 GB) — auxiliary text, not OCR.

Security: file operations restricted to configured roots (`BASE_DIR`/`BASE_DIRS`), symlinks escaping roots rejected, SQL-injection-safe predicates, 100 MB default file cap.

## Testing

Extensive Vitest suite (~60 test files, CPU and WebGPU profiles via `scripts/run-vitest-with-device.mjs`):

- **Unit**: semantic chunker, sentence splitter, parsers (PDF/DOCX/HTML/title extraction), search filters (grouping, boost), embedder (dtype handling, lazy init, batch outlier deferral), vectordb, scope matching, CLI.
- **Integration**: server-level ingest/search/delete/sync/read-neighbors/embedding, ingest rollback on failure, multi-root, content-hash reconciliation, FTS behavior, tool schemas.
- **E2E**: full rag-workflow, HTML ingestion, visual ingest, list-scope.
- **Perf**: read-neighbors latency guard.
- **Security**: path traversal / scope escape tests.

Tooling: Biome (lint + format), knip (unused exports), dpdm (circular deps), husky pre-commit/pre-push, GitHub Actions CI.