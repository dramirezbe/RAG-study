# AGENTS.md

## Repo state

Exploration folder for RAG MCP research. The source of truth for each app is its cloned upstream repository plus the three exploration artifacts per app (`LINK.md`, `ARCHITECTURE.md`, `HOW_TO_USE.md`) and the cross-app `COMPARE.md`.

## What lives here

- `explore-apps/clones/` — the **upstream clones** (each has its own `.git/` — never stage or commit changes into these nested repos; the whole directory is gitignored).
- `explore-apps/mcp-local-rag/` and `explore-apps/mcp-rag-server/` each hold the three exploration artifacts:
  - `LINK.md` — upstream URL and clone command,
  - `ARCHITECTURE.md` — RAG approach and why, tools used, chunking, vector database, testing,
  - `HOW_TO_USE.md` — installation and parametrizable parameters.
- `explore-apps/COMPARE.md` — side-by-side comparison and recommendation.
- Root `.gitignore` — ignores `**/copy/` and `explore-apps/clones/` (single consolidated ignore file since 2026-08-20).

## Working rules

- This folder is **not a git repo of its own**; it lives untracked inside the notebook-lifecycle-agent repo. The `explore-apps/clones/` embedded repos are gitignored — never `git add` their contents.
- The exploration artifacts must stay **outside** the clone dirs: git refuses to track files inside an embedded repo (it treats the whole dir as a gitlink). Keep artifacts at `explore-apps/<name>/`, clones at `explore-apps/clones/<name>/`.
- When exploring a new app: clone per its `LINK.md`, read the source (manifest, chunker, embedder, vector store, server entry, tests), then fill `ARCHITECTURE.md` and `HOW_TO_USE.md` before writing comparisons.
- Docs are written in English, derived from the actual source code, not from assumptions about the upstream README.
- Upstream repos are third-party and pinned to whatever commit was cloned; note the version in the docs when it changes.

## Commands

```bash
# Clone an app into explore-apps/clones/<name>/ (scaffold dirs at explore-apps/<name>/ already hold LINK.md etc.)
git clone <url> /tmp/opencode/<name> && cp -a /tmp/opencode/<name>/. explore-apps/clones/<name>/
```

## Gotchas

- The explore-app dirs were pre-seeded with 0-byte `ARCHITECTURE.md`/`HOW_TO_USE.md` scaffolds; filling them is the exploration deliverable, not copying upstream docs.
- mcp-rag-server ships no tests; claims about its behavior come from source reading and README, not a suite.
- Changing embedding model/device/dtype invalidates existing indexes in both apps (new `DB_PATH`/store or re-ingest required).