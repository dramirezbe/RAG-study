# README

Research folder comparing two RAG MCP servers side by side, based on source exploration of each repository.

## Layout

```
RAG-mcp-explore/
├── .gitignore                  # ignores **/copy/ and explore-apps/clones/
└── explore-apps/
    ├── COMPARE.md              # side-by-side comparison of both apps
    ├── clones/                 # upstream clones (embedded git repos, gitignored)
    │   ├── mcp-local-rag/      # clone of shinpr/mcp-local-rag
    │   └── mcp-rag-server/     # clone of Daniel-Barta/mcp-rag-server
    ├── mcp-local-rag/          # exploration docs
    │   ├── LINK.md             # upstream URL + clone command
    │   ├── ARCHITECTURE.md     # RAG design, chunking, vector DB, testing
    │   └── HOW_TO_USE.md       # install + parametrizable parameters
    └── mcp-rag-server/         # exploration docs
        ├── LINK.md             # upstream URL + clone command
        ├── ARCHITECTURE.md     # RAG design, chunking, vector DB, testing
        └── HOW_TO_USE.md       # install + parametrizable parameters
```

Each `explore-apps/<app>/` directory holds the three exploration artifacts: `LINK.md` (source), `ARCHITECTURE.md` (how the RAG works), and `HOW_TO_USE.md` (install + parameters). The upstream clones live separately in `explore-apps/clones/<app>/` and are gitignored.

## The apps in one line

- **mcp-local-rag** — fully local hybrid RAG: semantic Max–Min chunking, Transformers.js embeddings, LanceDB embedded, BM25 keyword boost, ~60 Vitest tests.
- **mcp-rag-server** — minimal RAG: fixed-size overlapping chunks, in-memory linear cosine scan (no vector DB), local or OpenAI-compatible embeddings, no test suite.

See `explore-apps/COMPARE.md` for the full comparison and a recommendation for the notebook-lifecycle-agent project.

## How the exploration docs were produced

1. Clone the upstream repo into `explore-apps/clones/<name>/` (see each `LINK.md`).
2. Read the source: README, package manifest, chunker, embedder, vector store, server entry, and test layout.
3. Write `ARCHITECTURE.md` (RAG approach and why, tools, chunking, vector database, testing) and `HOW_TO_USE.md` (install and parametrizable parameters) from that exploration.
4. Cross-compare both in `COMPARE.md`.