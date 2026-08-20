# Architectural Decision Guide: RAG Systems for Regulatory and Technical Documentation

**State of the Art — August 2026**

---

## 1. Purpose of the Document

This document serves as a **strategic evaluation framework**. Its goal is not to guide technical implementation, but to provide the criteria needed to decide **what kind of RAG architecture to build**, evaluating current industry philosophies (SOTA 2026), available tools, complexity levels, and the ideal user profile for each case.

---

## 2. SOTA Philosophy and Industry Trends (2026)

In the current landscape, extracting knowledge from complex PDFs (regulations, hierarchies, design diagrams) has moved beyond simple text chunking.

### Document Processing (SOTA Parsing)

The industry has shifted toward **native multimodal models and semantic parsers** (e.g., LlamaParse, Docling) that understand tables and complex structures without losing formatting.

### Hybrid Retrieval and GraphRAG

Vector similarity alone is no longer used. The current standard **combines keyword search, vectors, and knowledge graphs** to connect regulatory articles that reference each other.

### Context Standardization

The emergence of the **Model Context Protocol (MCP)** has changed the paradigm: instead of building monolithic RAG applications, the trend is to create **"context servers"** connectable to any AI client or agent.

---

## 3. Architecture Paradigm: Custom RAG vs. MCP Server

This section evaluates how to structure the solution at the software level.

### A. Custom RAG (Ad-Hoc Pipeline)

Builds the entire flow from scratch: ingestion, vector database, retrieval, LLM orchestration, and frontend.

| Attribute | Detail |
| --- | --- |
| **Philosophy** | Absolute control and extreme optimization for highly specific workflows. |
| **Complexity** | High. Requires constant pipeline maintenance and infrastructure management. |
| **Target user** | Companies with AI-core products that need their own user interfaces (UI), highly specific re-ranking logic, or deep integration with non-standard legacy systems. |
| **Advantages** | Total flexibility, frontend customization, millisecond-level latency optimization. |
| **Disadvantages** | High technical debt, slow development cycle (months), "reinventing the wheel" for integrations. |
| **Ready-made tools (ecosystem)** | LangChain, LlamaIndex, Haystack, Qdrant/Pinecone, Streamlit/Vercel (for UI). |
| **Compatibility** | Closed. Only communicates with the systems the development team explicitly programs. |

### B. RAG Server via MCP (Model Context Protocol)

Packages the retrieval system (your PDFs and regulations) as a server that exposes its resources under the MCP standard, allowing any MCP-compatible AI client to consume it.

| Attribute | Detail |
| --- | --- |
| **Philosophy** | "Write the context once, consume it anywhere." Interoperability and open ecosystem. |
| **Complexity** | Medium/Low. Orchestration and frontend are delegated to existing AI clients. |
| **Target user** | Internal teams, design studios, or law firms that already use third-party AI assistants and only need to connect their private data without building a new application from scratch. |
| **Advantages** | Ultra-fast deployment (days/weeks), zero effort building user interfaces, native integration with developer and analyst workflows. |
| **Disadvantages** | Functional dependency on the client tool (if the AI client reasons poorly, the RAG will fail); less control over the final prompt or the UI. |
| **Ready-made tools (ecosystem)** | Official MCP SDKs, Smithery.ai (server registry). |
| **Compatibility** | Open and universal with SOTA clients: Claude Desktop, Cursor, Windsurf, Zed, and any MCP-compatible AI agent. |

---

## 4. Infrastructure Paradigm: Local vs. API (Cloud)

This section evaluates where and how the AI models are processed and where the data is stored.

### A. Local Deployment (On-Premise / Edge)

Runs the embedding and generation models on your own hardware (company-owned local CPUs/GPUs).

| Attribute | Detail |
| --- | --- |
| **Target user** | Government entities, law firms, defense, or healthcare companies with strict privacy regulations (HIPAA, strict GDPR). |
| **Complexity** | Very High. Requires a DevOps/MLOps profile and costly hardware. |
| **Advantages** | <ul><li>**Absolute privacy:** regulatory data and design secrets never leave the machine.</li><li>**Fixed long-term cost** (no per-token payment).</li><li>**Immunity** to internet outages and provider rate limits.</li></ul> |
| **Disadvantages / Where it breaks** | <ul><li>**VRAM is the bottleneck:** scaling to multiple concurrent users quickly brings the machine down.</li><li>**Less capable models:** local LLMs (7B–70B parameters) often fail to match the deep reasoning required for complex legal/regulatory jargon compared with cloud models.</li></ul> |
| **Ready-made tools** | Ollama, vLLM, LM Studio, llama.cpp, HuggingFace TEI. |

### B. API / Cloud (SaaS)

Consumes foundation models hosted by third parties (OpenAI, Anthropic, Google, etc.).

| Attribute | Detail |
| --- | --- |
| **Target user** | Startups, agile companies, and teams that prioritize response quality and delivery speed over data concerns (provided B2B no-training agreements are signed). |
| **Complexity** | Low. The provider manages the hardware. |
| **Advantages** | <ul><li>**Absolute state-of-the-art reasoning** (2026 reasoning models, huge context windows).</li><li>**Infinite scalability:** supports spikes of 1,000 users without extra configuration.</li><li>**Highly optimized Time-to-First-Token (TTFT) latency.**</li></ul> |
| **Disadvantages / Where it breaks** | <ul><li>**Cost leaks:** if the regulations are very long and the RAG is inefficient, the bill for millions of tokens can skyrocket.</li><li>**Provider dependency:** global API outages affect your service.</li><li>**Legal/compliance barriers** if regulation explicitly forbids sending data to public clouds.</li></ul> |
| **Ready-made tools** | OpenAI API, Anthropic API, Google Vertex AI, AWS Bedrock. |

---

## 5. Decision-Making Summary Table

| Business Scenario | Architectural Recommendation | Infrastructure Recommendation |
| --- | --- | --- |
| **Internal legal audit:** a team reviews confidential regulations that cannot leave the network by law. They already use local assistants. | **MCP Server RAG** (to connect to their existing assistants) | **Local** (Ollama/vLLM) to guarantee total privacy |
| **B2B SaaS product:** they want to sell their own web platform to architects for consulting building regulations. | **Custom RAG** (they need their own UI, analytics, and flow control) | **API / Cloud** to scale users quickly and use SOTA models |
| **Developer/designer productivity:** the team needs technical design clarifications directly from their IDE (e.g., Cursor). | **MCP Server RAG** (integrates natively into their daily work environment) | **API / Cloud** (assuming the data is not government top-secret) |
