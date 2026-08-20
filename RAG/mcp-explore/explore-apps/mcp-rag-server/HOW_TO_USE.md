# mcp-rag-server — How to Use

Source: https://github.com/Daniel-Barta/mcp-rag-server (v0.4.x). Explored from the cloned repository at this path.

## Requirements

- Node.js **20 or later**
- A repository directory to index (`REPO_ROOT`)
- Internet access on first run in local mode (embedding model download, tens to ~100 MB)

## Install

```bash
npm install
npm run build        # tsup → dist/index.js
```

## Run (stdio transport, default)

**macOS / Linux**

```bash
export REPO_ROOT="/path/to/your-repo"
npm start
# or: node dist/index.js
```

**Windows PowerShell**

```powershell
$env:REPO_ROOT="C:\path\to\your-repo"; npm start
```

Optional: set a model cache so the model is downloaded once and reused:

```bash
export TRANSFORMERS_CACHE="/path/to/cache"      # macOS/Linux
$env:TRANSFORMERS_CACHE="C:\path\to\cache"     # PowerShell
```

You can also put settings in a `.env` file at the project root (copy `.env.example`).

## Run with OpenAI-compatible API embeddings

```bash
export REPO_ROOT="/path/to/your-repo"
export EMBEDDING_PROVIDER="openai"
export EMBEDDING_API_BASE_URL="https://api.openai.com/v1"   # base URL, NOT the /embeddings path
export EMBEDDING_API_KEY="<your-api-key>"
export MODEL_NAME="text-embedding-3-small"
npm start
```

Works with any provider exposing the OpenAI embeddings protocol (OpenAI, Mistral, Jina AI, ...).

## Run in HTTP mode (recommended for large repos)

```bash
export REPO_ROOT="/path/to/your-repo"; MCP_TRANSPORT=http npm start
```

- MCP endpoint: `http://127.0.0.1:3000/mcp`
- Readiness: `http://127.0.0.1:3000/health` → `{"ready": true, "indexing": {...}}` (ready only after all chunks are embedded)
- Assistant guidance: `http://127.0.0.1:3000/instructions` (serves `docs/copilot-instructions.md`, with `<FOLDER_INFO_NAME>` replaced)
- Wait for `ready: true` before connecting your IDE to avoid cold-start timeouts.

## Test with MCP Inspector

```bash
export REPO_ROOT="/path/to/your-repo"
npx @modelcontextprotocol/inspector node dist/index.js          # stdio
MCP_TRANSPORT=http npx @modelcontextprotocol/inspector http://127.0.0.1:3000/mcp --transport http
```

In the Inspector: List tools → `rag_query`, `read_file`, `list_files` → call with JSON, e.g.

```json
{ "query": "protobuf message X schema", "top_k": 5 }
```

## Parametrizable parameters

All settings are environment variables (or `.env`). Full list:

| Variable | Required | Default | Description |
|---|---|---|---|
| `REPO_ROOT` | yes | `C:/path/to/your/repository` (placeholder) | Absolute path of the repository to index |
| `FOLDER_INFO_NAME` | no | `REPO_ROOT` | Display label used in tool descriptions (cosmetic only) |
| `EMBEDDING_PROVIDER` | no | `local` | `local` or `openai` (OpenAI-compatible `/embeddings` API) |
| `MODEL_NAME` | no | `jinaai/jina-embeddings-v2-base-code` | Local HF model or remote model id, e.g. `Xenova/bge-base-en-v1.5`, `text-embedding-3-small` |
| `TRANSFORMERS_CACHE` | no | default HF cache | Cache folder for local model files |
| `EMBEDDING_API_BASE_URL` | if `openai` | — | API base, e.g. `https://api.openai.com/v1` |
| `EMBEDDING_API_KEY` | if `openai` | — | Bearer token for the embeddings API |
| `EMBEDDING_API_BATCH_SIZE` | no | `200` | Chunks per remote embeddings request |
| `ALLOWED_EXT` | no | ~30 common code/doc extensions + `pdf` | Comma-separated extensions to index (no leading dots) |
| `EXCLUDED_FOLDERS` | no | `node_modules,dist,build,.git,target,bin,obj,.cache,coverage,.nyc_output` | Exact folder names or glob patterns to skip (e.g. `**/test/**,**/tests/**`) |
| `CHUNK_SIZE` | no | `2400` (~800 tokens) | Max characters per chunk; clamped to 8000. Lower = finer matches, more vectors |
| `CHUNK_OVERLAP` | no | `400` (~120 tokens) | Trailing chars carried into next chunk; clamped to 4000; auto-reduced if >= size. Rule of thumb: 10–20% of `CHUNK_SIZE` |
| `INDEX_STORE_PATH` | no | unset | Persisted index base path (`.mcp-index.partNNNN.json` + manifest) for warm starts and incremental reindexing |
| `DOCS_PER_FILE` | no | `10000` | Max documents per persisted JSON file (min 100) |
| `MCP_TRANSPORT` | no | `stdio` | `stdio` or `http` |
| `HOST` | no (HTTP) | `127.0.0.1` | HTTP bind host |
| `MCP_PORT` | no (HTTP) | `3000` | HTTP port |
| `ENABLE_DNS_REBINDING_PROTECTION` | no (HTTP) | `true` | Set `false` to disable host allow-list checks |
| `ALLOWED_HOSTS` | no (HTTP) | localhost/127.0.0.1 (+ports) | Comma-separated hosts allowed with DNS rebinding protection |
| `VERBOSE` | no | off | `1/true/yes/on` → granular progress logs (stderr) |

## Tuning guidance

- **Chunk size**: short functions/config files → smaller chunks (1200–1800); large prose/specs → larger (3000–4200); heavily interdependent code → keep default or raise overlap to ~500–600 chars. Avoid sizes < 300 without a reranking stage.
- **Model**: `jinaai/jina-embeddings-v2-base-code` for mixed code + docs; `Xenova/bge-base-en-v1.5` for English docs; `Xenova/bge-small-en-v1.5` when latency/memory matter; `text-embedding-3-small` (preferred) / `-large` via API.
- Changing `EMBEDDING_PROVIDER` or `MODEL_NAME` **invalidates a persisted index** on purpose (manifest compatibility check).
- Force a full rebuild by deleting the manifest + part files, or changing chunk/model parameters.

## Known limitations

- No test suite; verify with MCP Inspector / manual runs.
- Local embedding is sequential (one chunk at a time) — first build of large repos takes minutes (logs go to stderr).
- Incremental change detection uses **file size only**; same-size edits are not re-embedded.