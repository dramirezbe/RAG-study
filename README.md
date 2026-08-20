# RAG-study

Research repository on **Retrieval-Augmented Generation (RAG)** for regulatory and technical documentation, focused on FM broadcast spectrum regulation (FCC, ANE Colombia, ITU-R). Developed at Universidad Nacional de Colombia.

The repo covers the full research arc: conceptual foundations, SOTA studies, tool exploration, a working MCP-based RAG implementation, and an applied use case (RAG-informed LaTeX engineering specification).

## Repository map

| Area | Contents | Status |
| --- | --- | --- |
| [`RAG/explanation/`](RAG/explanation/) | Conceptual RAG treatise (LaTeX report), teaching notebook with regulatory corpus (FM spectrum), RAG-ready normalized corpus documents | Stable reference |
| [`RAG/implementation/`](RAG/implementation/) | **MCP-based local RAG server PoC**: notebook (`local-LLM-RAG.ipynb`), Chroma index (generated, not tracked), QA run artifacts, architectural decision guide (`context/CONTEXT.md`), MCP solution proposal (`context/ideas_solutions/SOLUTION_SERVER_MCP.md`) | Working PoC |
| [`RAG/Design-FMSensor/`](RAG/Design-FMSensor/) | Applied use case: LaTeX spec for an SDR-based FM compliance monitoring system, maintained through RAG-informed HITL refactor passes (LAP1 done, LAP2 pending). Has its own `AGENTS.md` | Active document project |
| [`RAG/mcp-explore/`](RAG/mcp-explore/) | Source-level exploration of two RAG MCP servers (`mcp-local-rag` vs `mcp-rag-server`) with comparison and recommendation | Exploration complete |
| [`agents/`](agents/) | Agentic architecture research: SDD methodology notes (`sdd.md`), agent evolution essay (`agents-evolution.md`), SOTA papers for `DesignAgentOrchestrator`, SDD workshop tutorial (`TutorialSDDAgents/`) | Research/teaching |
| [`prompting/`](prompting/) | Prompt engineering work: MarkItDown tutorial + slide deck (`PromptEngineering/`), notebook prompt experiments across GPT/Claude/Gemini/Kimi/DeepSeek/Qwen (`notebook-python/`) | Experiments |
| [`LLMs/`](LLMs/) | SOTA study notes: LLM evaluation benchmarks (`benchmarks/`), context-window solutions (`context-window/`) | Study notes |
| [`hw/`](hw/) | Hardware constraints of the local dev machine (Dell Precision 7520, CPU-only inference baseline) for local AI experiments | Reference |
| [`.agents/skills/`](.agents/skills/) | Project-scoped agent skills: excalidraw-diagram, jupyter-notebook-editor, skill-creator, sw-development-proposal, vite | Tooling |

## How the areas connect

```
LLMs/ + agents/ + prompting/   →  conceptual and SOTA research
        ↓
RAG/explanation/               →  the theory, as a LaTeX treatise + corpus
        ↓
RAG/mcp-explore/               →  tool survey (MCP RAG servers)
        ↓
RAG/implementation/            →  working local RAG PoC over the regulatory corpus
        ↓
RAG/Design-FMSensor/           →  applied use: RAG-informed engineering spec (LAP1 verified)
```

## Key entry points

- **Architecture decision guide**: `RAG/implementation/context/CONTEXT.md` — custom RAG vs MCP server, local vs cloud.
- **MCP solution proposal**: `RAG/implementation/context/ideas_solutions/SOLUTION_SERVER_MCP.md` — why MCP is the experimental boundary for local-vs-API comparison.
- **Working implementation**: `RAG/implementation/notebooks/local-LLM-RAG.ipynb` (CPU-only, Ollama + Chroma + all-MiniLM-L6-v2).
- **Applied case**: `RAG/Design-FMSensor/README.md` — build instructions, RAG corpus mapping (`context/SCOPE-RAG.md`), refactor plans.
- **Agent guidance per area**: each sub-project carries its own `AGENTS.md`.

## Notes

- **Language mix**: deliverables/docs are in English; some study notes (LLMs/, agents/ essays, SDD notes) are in Spanish.
- **External RAG index**: the FM regulatory corpus lives in a local-rag server index at `/home/javastral/RAG-documents/` (outside this repo). `RAG/Design-FMSensor/docs-RAG-FM/` holds human-readable copies only.
- **Generated artifacts** (Chroma indexes, LanceDB dirs, skill-registry caches) are gitignored — see `AGENTS.md`.