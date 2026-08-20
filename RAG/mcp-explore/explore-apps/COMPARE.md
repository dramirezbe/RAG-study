# mcp-local-rag vs mcp-rag-server — Comparison

Two RAG MCP servers explored from source (see `mcp-local-rag/ARCHITECTURE.md` + `HOW_TO_USE.md` and `mcp-rag-server/ARCHITECTURE.md` + `HOW_TO_USE.md` for the full exploration).

| | **mcp-local-rag** (shinpr) | **mcp-rag-server** (Daniel-Barta) |
|---|---|---|
| Version / license | v0.17.4, MIT, npm-published | v0.4.0, MIT, private (build from source) |
| Runtime | Node ≥ 22, TypeScript ESM | Node ≥ 20, TypeScript ESM |
| Install | `npx -y mcp-local-rag` (zero install) | `npm install && npm run build` |
| Target corpus | Private documents (PDF, DOCX, TXT, MD, HTML) | Source code + docs of a repository |
| Transport | MCP stdio only | MCP stdio or Streamable HTTP (Express, `/health`, `/instructions`) |

## RAG approach

| Aspect | mcp-local-rag | mcp-rag-server |
|---|---|---|
| **Design philosophy** | Production-grade, feature-rich, local-first | Deliberately minimal and simple |
| **Embedding provider** | Local only (Transformers.js / ONNX). No API key ever | Local (Transformers.js) **or** OpenAI-compatible API |
| **Privacy** | Fully offline after model download; no network at query time | Local mode offline after download; API mode sends chunks to a remote endpoint |
| **Default model** | `Xenova/all-MiniLM-L6-v2` (~90 MB, 384-dim) | `jinaai/jina-embeddings-v2-base-code` (q8 quantized) |
| **Search** | **Hybrid**: vector + BM25 keyword boost | **Pure vector**: linear cosine scan, no reranking |
| **Why** | Semantic search alone misses exact technical identifiers; hybrid keeps both intent and literal terms | Predictability and minimal deps for medium repos; hybrid BM25 and ANN explicitly deferred as future work |
| **Maturity** | Hybrid, sync, PDF visual mode, agent skills, CI | Prototype-grade; roadmap items listed in README |

## Chunking

| Aspect | mcp-local-rag | mcp-rag-server |
|---|---|---|
| **Strategy** | **Semantic** (Max–Min algorithm, Springer 2025) | **Fixed-size with overlap** (naïve character slicing) |
| **Mechanism** | Split into sentences → embed each → greedy grouping: join chunk if max similarity to members > dynamic threshold `max(c·minSim·sigmoid(|C|), hardThreshold)` | `text.slice(i, i + size)`, step `size − overlap`, single pass |
| **Parameters** | `hardThreshold 0.6`, `initConst 1.5`, `c 0.9`, min chunk 50 chars, window 5, max 15 sentences, garbage-chunk filter | `CHUNK_SIZE 2400` (~800 tokens), `CHUNK_OVERLAP 400` (~15%), clamped 8000/4000, overlap≥size auto-fixed |
| **Boundaries** | Topic boundaries; Markdown code blocks preserved as atomic ranges | Cuts anywhere, including mid-sentence / mid-code (acknowledged limitation) |
| **Overlap** | None (semantic coherence instead) | 400 chars, to retain cross-boundary context |
| **Cost** | Extra embedding pass over sentences at ingest | Cheapest possible (O(n) slicing) |

## Vector database

| Aspect | mcp-local-rag | mcp-rag-server |
|---|---|---|
| **Storage** | **LanceDB embedded** (local folder `./lancedb/`, no server) | **None — in-memory array** of chunks + `Float32Array` vectors |
| **Index** | Vector (dot product over L2-normalized = cosine) + **ngram FTS** (min 2, max 3, CJK-capable) | Nothing; linear scan per query (O(n)) |
| **Persistence** | Native LanceDB (schema: filePath, chunkIndex, text, metadata, vector, fileTitle, contentHash, timestamp) | Optional JSON part files + manifest (`INDEX_STORE_PATH`) |
| **Incremental sync** | Content-hash based (`sync_start` skips byte-identical, prunes deleted) | File-**size**-based only (same-size edits missed) |
| **Query time** | ANN-style vector search + FTS + rerank pipeline | Linear scan — degrades with repo size |
| **Backup** | Copy `DB_PATH` folder | Copy JSON files |

## Search pipeline

| Step | mcp-local-rag | mcp-rag-server |
|---|---|---|
| 1 | Embed query (same model) | Embed query (same model) |
| 2 | Vector search, `limit × 2` candidates | Cosine against all chunks |
| 3 | Optional scope prefix filter, `maxDistance` | — |
| 4 | Optional relevance-group filter (mean + 1.5σ gaps: `similar`/`related`) | — |
| 5 | **Keyword boost** (BM25 normalized × weight 0.6, multiplicative) | — |
| 6 | Optional top-N-files filter | — |
| 7 | Top-k | Top-k (default 5, max 50) |

## MCP tools

| mcp-local-rag | mcp-rag-server |
|---|---|
| `sync_start`, `sync_status` | — |
| `ingest_file`, `ingest_data` (text/MD/HTML) | — |
| `query_documents` (hybrid, scope, neighbors) | `rag_query` (pure semantic, top_k) |
| `read_chunk_neighbors` (surrounding context) | `read_file` (whole file or line range) |
| `list_files`, `delete_file`, `status` | `list_files` |

mcp-local-rag's model operates on **chunks with provenance**; mcp-rag-server returns snippets and lets the client fetch the exact source file — a different but complementary division of labor.

## Testing

| Aspect | mcp-local-rag | mcp-rag-server |
|---|---|---|
| **Suite** | ~60 Vitest files: unit (chunker, splitter, parsers, search filters, embedder), integration (ingest/search/delete/sync/neighbors, rollback, multi-root, content-hash), e2e (rag workflow, HTML, visual), perf, security | **None** |
| **Checks** | Biome lint/format, knip, dpdm (circular deps), husky hooks, GitHub Actions CI | Only `tsc --noEmit`, ESLint, Prettier |
| **Verification** | Automated, deterministic gates | Manual via MCP Inspector |

## Security

| Aspect | mcp-local-rag | mcp-rag-server |
|---|---|---|
| File boundary | `BASE_DIR`/`BASE_DIRS` roots; symlink escapes rejected | `REPO_ROOT` via `ensureWithinRoot` (traversal guard); absolute paths rejected |
| HTTP | n/a (stdio only) | DNS rebinding protection + host allow-list (default on) |
| Injection | SQL-injection-safe predicates, size caps (100 MB) | Path normalization handled; `read_file` can read any file inside root |

## Verdict — which to choose

- **mcp-local-rag** — choose when you need **private document search** (PDF/DOCX/HTML), retrieval quality (semantic chunking + hybrid reranking), a real vector DB (LanceDB) with content-hash sync, and **provable correctness** (test suite + CI). Costs: Node 22+, heavier (~90 MB model, LanceDB), stdio only, no remote embedding option.
- **mcp-rag-server** — choose for **code-corpus Q&A in an IDE** (Copilot Agent mode), quick setup, HTTP transport with health/instructions endpoints, and the option to plug a remote embeddings API. Accept: fixed-size chunking, linear-scan recall limits on big repos, no tests.

For a project like notebook-lifecycle-agent (Python notebooks, quality-sensitive, CI-gated), **mcp-local-rag is the stronger foundation**; mcp-rag-server is useful as a minimal reference for how to wire MCP + embeddings without a vector DB.