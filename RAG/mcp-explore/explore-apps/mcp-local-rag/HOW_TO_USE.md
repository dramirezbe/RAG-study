# mcp-local-rag — How to Use

Source: https://github.com/shinpr/mcp-local-rag (v0.17.x, MIT). Explored from the cloned repository at this path.

## Requirements

- Node.js **22 or later**
- Internet access **on first use** (npm package + embedding model download ~90 MB)
- A directory with the documents to index (`BASE_DIR`), which is also the security boundary

After the initial model download, ingestion and search work fully offline.

## Install / run as an MCP server

There is nothing to install: the server is run via `npx`. It speaks the standard MCP protocol over a local **stdio** server and works with any MCP-capable client.

**Claude Code**

```bash
claude mcp add local-rag --scope user --env BASE_DIR=/absolute/path/to/your/documents -- npx -y mcp-local-rag
```

**OpenCode** (`~/.config/opencode/opencode.json`)

```json
{
  "mcp": {
    "local-rag": {
      "type": "local",
      "command": ["npx", "-y", "mcp-local-rag"],
      "environment": { "BASE_DIR": "/absolute/path/to/your/documents" }
    }
  }
}
```

**Codex** (`~/.codex/config.toml`)

```toml
[mcp_servers.local-rag]
command = "npx"
args = ["-y", "mcp-local-rag"]

[mcp_servers.local-rag.env]
BASE_DIR = "/absolute/path/to/your/documents"
```

**Cursor** (`~/.cursor/mcp.json`)

```json
{
  "mcpServers": {
    "local-rag": {
      "command": "npx",
      "args": ["-y", "mcp-local-rag"],
      "env": { "BASE_DIR": "/absolute/path/to/your/documents" }
    }
  }
}
```

Restart the client, then ask it to sync: *"Sync all documents in the configured root and wait until it finishes."* First sync downloads the model and takes 1–2 minutes; later runs use the local cache.

## CLI usage (no MCP client)

```bash
npx mcp-local-rag ingest ./docs/          # ingest a directory
npx mcp-local-rag sync ./docs/            # reconcile index with disk
npx mcp-local-rag query "authentication API"
npx mcp-local-rag query "auth" --scope /docs/api --scope /docs/guide
npx mcp-local-rag read-neighbors --file-path /abs/path.md --chunk-index 5
npx mcp-local-rag list
npx mcp-local-rag status
npx mcp-local-rag delete ./docs/old.pdf
npx mcp-local-rag delete --source "https://example.com/docs"
```

Global options (`--db-path`, `--cache-dir`, `--model-name`, ...) go **before** the subcommand; subcommand options after it. Default document root is the current directory.

## Parametrizable parameters

The MCP server reads environment variables; the CLI accepts the same settings as flags (flags take precedence).

| Env variable | CLI flag | Default | Description |
|---|---|---|---|
| `BASE_DIR` | `--base-dir` | current dir | Document root; security boundary for file ops (repeatable on `ingest`/`list`/`sync`) |
| `BASE_DIRS` | — | unset | JSON array of roots, e.g. `'["/a","/b"]'`; takes precedence over `BASE_DIR` |
| `DB_PATH` | `--db-path` | `./lancedb/` | Vector database location |
| `CACHE_DIR` | `--cache-dir` | `./models/` | Model cache directory |
| `MODEL_NAME` | `--model-name` | `Xenova/all-MiniLM-L6-v2` | Hugging Face embedding model |
| `MAX_FILE_SIZE` | `--max-file-size` | `104857600` (100 MB) | Max file size in bytes |
| `CHUNK_MIN_LENGTH` | `--chunk-min-length` | `50` | Min chunk length in chars (1–10000) |
| `RAG_DEVICE` | — | `cpu` | ONNX Runtime execution device |
| `RAG_DTYPE` | — | `fp32` | Embedding dtype passed to the model |
| `RAG_HYBRID_WEIGHT` | — | `0.6` | Keyword boost factor (0.0–1.0); `0` disables keyword reranking |
| `RAG_GROUPING` | — | unset | `similar` keeps 1 relevance group; `related` keeps up to 2 (significant vector-distance gaps as boundaries) |
| `RAG_MAX_DISTANCE` | — | unset | Drop low-relevance results (e.g. `0.5`) |
| `RAG_MAX_FILES` | — | unset | Limit results to top N files (e.g. `1` for the single best file) |

Precedence for roots: CLI `--base-dir` flags → `BASE_DIRS` → `BASE_DIR` → current directory.

## Parameter tuning notes

- **Exact-term search** (APIs/specs full of identifiers): raise `RAG_HYBRID_WEIGHT` to `0.7`–`1.0` for stronger keyword reranking.
- **Model choice**: `Xenova/bge-small-en-v1.5` or `Xenova/bge-base-en-v1.5` for English docs. Models are used with **mean pooling + L2 normalization** — check the model's recommended inference setup. Changing `MODEL_NAME`/`RAG_DEVICE`/`RAG_DTYPE` makes existing vectors incompatible: use a new `DB_PATH` or delete the index and re-ingest.
- **PDF figures** (opt-in): `ingest_file` with `visual: true` (MCP) or `--visual` (CLI); `--visual-quality quality` for in-image text (~2.9 GB model vs ~250 MB `fast`).

## Ops notes

- Do not run multiple writers against the same `DB_PATH`; read-only queries can run during a sync.
- Back up an index by copying the `DB_PATH` directory while no writer is active.
- HTML is not fetched by the server: a client fetches pages and passes them to `ingest_data` (cleaned with Readability, stored under a stable `source` id; re-using the id updates the content).
- `status` reports index stats and whether search mode is `hybrid` or `vector-only`.