# SOTA Solutions for Context Window in LLMs

## Long Context (1M+ tokens)

**Main references:**
— Google DeepMind (2024). *Gemini 1.5: Unlocking multimodal understanding across millions of tokens of context.* [arXiv:2403.05530](https://arxiv.org/abs/2403.05530).
— Liu et al. (2024). *World Model on Million-Length Video and Language with RingAttention.* [arXiv:2402.08268](https://arxiv.org/abs/2402.08268).
— Anthropic (2024). *The Claude 3 Model Family: Opus, Sonnet, Haiku.* Model Card.
— Peng et al. (2023). *YaRN: Efficient Context Window Extension of Large Language Models.* [arXiv:2309.00071](https://arxiv.org/abs/2309.00071).

**Description:** The race to extend the context window of LLMs has gone from tens of thousands to millions of tokens in less than two years. Current SOTA models (Gemini 1.5 Pro, Claude 3, GPT-4 Turbo) offer 128K–1M token contexts, enabling applications such as analysis of entire repositories, processing of long videos, and reasoning over massive document corpora. The main strategies for reaching these lengths include RoPE scaling ([YaRN](https://arxiv.org/abs/2309.00071), NTK-aware, Self-Extend), distributed block attention ([RingAttention](https://arxiv.org/abs/2310.01889)), and hybrid architectures.

**Main characteristics:**

- **Current maximum capacity:** Gemini 1.5 Pro supports up to 10M tokens (closed beta) and 1M tokens in general availability, with sustained quality on needle-in-a-haystack (>99% recall at 1M tokens). Claude 3 offers 200K tokens with recall >98%.
- **Predominant extension technique:** [YaRN](https://arxiv.org/abs/2309.00071) (Yet another RoPE extensioN) and its variants (NTK-aware, NTK-by-parts, Dynamic YaRN) are the most adopted approach for RoPE-based models (Llama 2/3, Mistral, Qwen). They require only 400–1000 fine-tuning steps.
- **Self-Extend (Jin et al., 2024):** Zero-shot extension without fine-tuning using grouped attention at the floor level — nearby tokens are attended to with full precision while distant ones are grouped. Effective up to 4× the original training length.
- **Native long training (Anthropic):** Claude 3 trains directly on long sequences without relying on post-hoc interpolation, using proprietary distributed training techniques.
- **Limitations:** Although positional recall is high, models still degrade on multi-hop reasoning and synthesis tasks over long contexts. Computational cost scales with length (although [FlashAttention-3](https://arxiv.org/abs/2407.08608) reduces the overhead to O(N log N)).
- **Standard evaluation:** *Needle-in-a-Haystack* (NIAH), *RULER* (synthetic long-context tasks), *LOFT* (Long-Context Frontiers), and *LongBench*.

**Purpose:** Enable LLMs to process, reason over, and synthesize information from extremely long documents (entire books, codebases, hours of video) in a single pass, without segmentation, intermediate summarization, or external retrieval.

---

## RAG (Retrieval-Augmented Generation)

**Reference:** Lewis et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks.* NeurIPS 2020. [arXiv:2005.11401](https://arxiv.org/abs/2005.11401).
— Gao et al. (2023). *Retrieval-Augmented Generation for Large Language Models: A Survey.* [arXiv:2312.10997](https://arxiv.org/abs/2312.10997).

**Description:** Paradigm that combines a generative LLM with an external retrieval system (*retriever*). Instead of encoding all knowledge or context into the model's window, the system indexes documents in a vector knowledge base, retrieves the most relevant chunks for each query, and injects them into the LLM prompt. This enables access to massive volumes of information without saturating the context window.

**Main characteristics:**

- **Architecture:** 3-stage pipeline: (1) *indexing* — documents are split into chunks, encoded as embeddings, and stored in a vector database; (2) *retrieval* — given a query, the $k$ most similar chunks are retrieved; (3) *generation* — the LLM receives the query + retrieved chunks and generates the answer.
- **RAG modalities:**
  - **Naive RAG:** Simple retrieval + direct generation without additional processing.
  - **Advanced RAG:** Includes chunk re-ranking, query rewriting, and pre-generation relevance filtering.
  - **Modular RAG:** Interchangeable components (hybrid retrievers, memory, domain adapters, post-hoc verification).
- **Complement to long context:** RAG and long context are not mutually exclusive. Models with 128K+ contexts benefit from RAG because multiple chunks fit in a single window, reducing the number of retriever calls. *LongRAG* (Jiang et al., 2024) uses 4K-token chunks instead of the typical 100–200 tokens.
- **Graph RAG (Microsoft, 2024):** Extends RAG with LLM-generated knowledge graphs (*graph indexing*) that capture relationships between entities and topics, enabling multi-hop reasoning and global thematic synthesis beyond local vector similarity.
- **Agentic RAG:** Incorporates agents that iterate between retrieval and generation, evaluate the sufficiency of the information, reformulate queries, and decide when to search for more data.

**Purpose:** Overcome the limitations of static knowledge and the finite window of LLMs: access up-to-date, domain-specific, or private information without fine-tuning, while maintaining factual fidelity through source attribution.

**Citation (arXiv):**
- **Original RAG:** [arXiv:2005.11401](https://arxiv.org/abs/2005.11401)
- **Survey:** [arXiv:2312.10997](https://arxiv.org/abs/2312.10997)

---

## Fine-tuning for Context Extension

**Reference:** Chen et al. (2023). *Extending Context Window of Large Language Models via Positional Interpolation.* (Position Interpolation, PI). [arXiv:2306.15595](https://arxiv.org/abs/2306.15595).
— Peng et al. (2023). *YaRN: Efficient Context Window Extension of Large Language Models.* [arXiv:2309.00071](https://arxiv.org/abs/2309.00071).
— Xiong et al. (2023). *Effective Long-Context Scaling of Foundation Models.* (Llama 2 Long). [arXiv:2309.16039](https://arxiv.org/abs/2309.16039).
— Fu et al. (2024). *Data Engineering for Scaling Language Models to 128K Context.* (Long Data Curriculum). [arXiv:2402.10171](https://arxiv.org/abs/2402.10171).

**Description:** Long-context fine-tuning aims to adapt a pre-trained LLM (typically at 2K–8K tokens) to 32K–128K+ token windows through modifications to positional embeddings and continued training with long-sequence data. The techniques are divided into: (1) *positional interpolation* (continuous or piecewise adjustment of RoPE rotations), (2) *data engineering* (mixing and curriculum of long/short data), and (3) *training efficiency* (techniques that reduce fine-tuning cost).

**Main characteristics:**

- **Position Interpolation (PI, Meta 2023):** Linearly reduces the rotational frequencies so extended positions fit within the original training range. Simple but degrades short distances. [arXiv:2306.15595](https://arxiv.org/abs/2306.15595)
- **YaRN (Nous Research, 2023):** Combines NTK-aware interpolation (high frequencies keep resolution, low ones get compressed), by-parts partitioning (different factors depending on wavelength), and dynamic scaling at inference (softmax temperature adjustment). Gold standard: 128× extension with ~400–1000 steps. [arXiv:2309.00071](https://arxiv.org/abs/2309.00071)
- **Llama 2 Long (Meta, 2023):** Continued fine-tuning with NTK-adjusted RoPE + a data mix of 90% short / 10% long to preserve short-context capabilities. Effective up to 32K tokens. [arXiv:2309.16039](https://arxiv.org/abs/2309.16039)
- **Long Data Curriculum (Fu et al., 2024):** Shows that the key is not only the positional technique but also the quality and progression of the fine-tuning data: gradually scaling length (curriculum) and maintaining domain diversity avoids PPL collapse on long sequences. [arXiv:2402.10171](https://arxiv.org/abs/2402.10171)
- **SFT + DPO for long-context:** Supervised fine-tuning (SFT) is complemented with DPO (Direct Preference Optimization) using preference pairs where the correct answer requires attending to the entire context, training the model to *use* the extended window effectively, not just to avoid collapsing.
- **LoRA / QLoRA:** Low-rank adapters enable long-context fine-tuning with drastically reduced GPU memory (e.g., 1×A100 for Llama-2-7B at 32K with QLoRA vs 8×A100 for full fine-tuning).

**Purpose:** Adapt existing pre-trained models to extended contexts while preserving their prior capabilities, minimizing the computation required, and ensuring the model effectively *uses* the extended window in real tasks (not just that it does not collapse in perplexity).

**Citation (arXiv):**
- **Position Interpolation:** [arXiv:2306.15595](https://arxiv.org/abs/2306.15595)
- **YaRN:** [arXiv:2309.00071](https://arxiv.org/abs/2309.00071)
- **Llama 2 Long:** [arXiv:2309.16039](https://arxiv.org/abs/2309.16039)
- **Long Data Curriculum:** [arXiv:2402.10171](https://arxiv.org/abs/2402.10171)

---

## Memory Layers

**Reference:** Burtsev et al. (2020). *Memory Transformer.* [arXiv:2006.11527](https://arxiv.org/abs/2006.11527).
— Wang et al. (2024). *MemGPT: Towards LLMs as Operating Systems.* [arXiv:2310.08560](https://arxiv.org/abs/2310.08560).
— Munkhdalai et al. (2024). *Leave No Context Behind: Efficient Infinite Context Transformers with Infini-attention.* (Google). [arXiv:2404.07143](https://arxiv.org/abs/2404.07143).
— Liu et al. (2024). *Augmenting Language Models with Long-Term Memory.* (LongMem). NeurIPS 2023. [arXiv:2306.07174](https://arxiv.org/abs/2306.07174).
— Gu & Dao (2023). *Mamba: Linear-Time Sequence Modeling with Selective State Spaces.* [arXiv:2312.00752](https://arxiv.org/abs/2312.00752).

**Description:** Memory layers incorporate persistent memory components into the LLM architecture that allow storing and retrieving information beyond the immediate attention window. Unlike the KV-cache (which only covers the active context of the session), these memories persist across sessions, documents, and tasks, enabling true long-term memory in the model.

**Main characteristics:**

- **Memory Transformer (2020):** Adds persistent memory tokens concatenated to the input sequence that act as a read/write register across the context. These tokens memorize information from previous segments and make it available to future segments, extending the effective context beyond the attention window. [arXiv:2006.11527](https://arxiv.org/abs/2006.11527)
- **Infini-attention (Google, 2024):** Combines local attention (sliding window) with global compressive memory using linear attention: each new segment updates a fixed compressive state (memory matrix + normalization), which is then queried together with local attention. O(N) memory complexity, enabling "infinite" context on a single GPU. [arXiv:2404.07143](https://arxiv.org/abs/2404.07143)
- **MemGPT (UC Berkeley, 2024):** Implements virtual memory inspired by operating systems: the model explicitly manages a *main context* (active window) and an *external context* (persistent storage), moving data between them through explicit function calls (write/read/search). The model *learns* when to store and retrieve from its external memory. [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- **LongMem (NeurIPS 2023):** Architecture with an external memory bank (key-value store) coupled via a *side-network* to the frozen model. Memory is read through cross-attention between the current decoder state and the stored keys, and updated incrementally. It enables attending to information from arbitrarily long contexts (up to 65K tokens demonstrated) without increasing the cost of primary attention. [arXiv:2306.07174](https://arxiv.org/abs/2306.07174)
- **State Space Models (Mamba, 2023):** Reformulate memory as a selective recurrent state with O(N) complexity. Although not strictly *memory layers*, they incorporate implicit sequential compression that works as built-in compressive memory, extrapolating up to 1M tokens without perplexity degradation. [arXiv:2312.00752](https://arxiv.org/abs/2312.00752)
- **Main limitation:** The quality of compressive memory is inferior to exact attention for tasks that require precise recall of fine details in distant contexts. Current research seeks to close this gap with hybrid mechanisms (attention + compression).

**Purpose:** Break the quadratic context barrier by adding persistent memory to the model, allowing it to maintain and access information across sessions and documents without having to re-process the entire history at each inference step.

**Citation (arXiv):**
- **Infini-attention:** [arXiv:2404.07143](https://arxiv.org/abs/2404.07143)
- **MemGPT:** [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- **LongMem:** [arXiv:2306.07174](https://arxiv.org/abs/2306.07174)

---

## Vector Stores

**Reference:** Johnson et al. (2019). *Billion-scale similarity search with GPUs.* (FAISS, Meta). [arXiv:1702.08734](https://arxiv.org/abs/1702.08734).
— Douze et al. (2024). *The Faiss library.* (FAISS v2). [arXiv:2401.08281](https://arxiv.org/abs/2401.08281).
— Wang et al. (2021). *Milvus: A Purpose-Built Vector Data Management System.* (Milvus). SIGMOD 2021.
— Jin et al. (2024). *ColBERT: Efficient and Effective Passage Search via Contextualized Late Interaction over BERT.* (ColBERT, multi-vector). [arXiv:2004.12832](https://arxiv.org/abs/2004.12832).
— Günther et al. (2024). *Jina Embeddings v3.* (Jina AI, late-chunking).

**Description:** Vector stores (vector databases) are systems specialized in storing and searching over high-dimensional vector representations (*embeddings*). They constitute the indexing and retrieval component in RAG pipelines and are essential for scaling information access beyond what fits in the context window. They allow indexing millions or billions of documents and retrieving the most relevant ones in milliseconds via approximate nearest neighbor search (ANN).

**Main characteristics:**

- **ANNS indexing algorithms:**
  - **HNSW (Hierarchical Navigable Small World):** Multi-layer graph structure with search complexity O(log N). Optimal balance between speed and recall for most use cases (used by Milvus, Qdrant, Weaviate).
  - **IVF-PQ (Inverted File + Product Quantization):** Vector compression to reduce memory. [FAISS](https://arxiv.org/abs/2401.08281) implements GPU variants that achieve sub-millisecond searches over billions of vectors.
  - **DiskANN (Microsoft):** Indexing with SSD support for massive storage at low cost and acceptable latency.
- **Chunking strategies:** Document partitioning defines the retrieval unit:
  - **Fixed-size chunking:** Fixed size with overlap (typically 256–512 tokens with 10–20% overlap). Simple and robust.
  - **Semantic chunking:** Division by semantic boundaries (paragraphs, sections) using embeddings of adjacent sentences and similarity thresholds.
  - **Late chunking (Jina AI, 2024):** The embedding is computed over the full document (global context) and then segmented; the resulting representations retain contextual information that would be lost with naive chunking.
  - **ColBERT-style multi-vector:** Each token of the chunk has its own embedding; query-document similarity is computed as late interaction (MaxSim) between individual vectors, capturing token-level relevance. [arXiv:2004.12832](https://arxiv.org/abs/2004.12832)
- **Popular vector stores:**
  - **FAISS (Meta):** Lower-level C++/Python library, maximum efficiency and control over indexes. Not a full database. [arXiv:2401.08281](https://arxiv.org/abs/2401.08281)
  - **Milvus:** Cloud-native distributed vector database with support for horizontal scaling, multiple index types, metadata filtering, and integration with ETL pipelines.
  - **Qdrant:** Written in Rust, high performance, rich REST/gRPC API, support for payload filtering and hybrid search (dense + sparse/BM25).
  - **Weaviate:** Native integration with LLMs via modules (generative search), hybrid vector + keyword support, and GraphQL schema.
- **Retrieval strategies:**
  - **Dense retrieval:** Dense embeddings (e.g., OpenAI text-embedding-3, Cohere Embed v3, Jina Embeddings v3) for semantic retrieval. Captures meaning but can fail on exact matching (names, codes).
  - **Sparse/hybrid retrieval:** Combination of BM25 (exact keyword matching) with dense embeddings to cover both semantic and terminological queries.
  - **Multi-stage retrieval:** Embedding → coarse retrieval (large top-K, e.g., 100) → cross-encoder re-ranker (more accurate but expensive model) → final top-K (e.g., 5). Significantly improves accuracy.
  - **Multi-representation indexing (ColBERT):** Documents indexed as multiple per-token vectors with MaxSim search; more accurate than single-vector but more expensive in storage. [arXiv:2004.12832](https://arxiv.org/abs/2004.12832)

**Purpose:** Provide the semantic retrieval infrastructure that allows LLMs to access massive volumes of external knowledge with millisecond latency, constituting the most mature and widely adopted alternative for overcoming the context window limitation.

**Citation (arXiv):**
- **FAISS:** [arXiv:2401.08281](https://arxiv.org/abs/2401.08281)
- **ColBERT:** [arXiv:2004.12832](https://arxiv.org/abs/2004.12832)

---

## Comparison: Fine-tuning vs RAG vs Plugins

It is very common to think that fine-tuning is the definitive solution for an LLM to "learn" about your business or private data, but the reality is that it is almost always the wrong tool for that purpose. To inject knowledge or give superpowers to a model, methodologies such as RAG (Retrieval-Augmented Generation) or the use of Skills/Plugins (tools/function calls) are usually much more efficient.

### 1. The "Black Box" problem and hallucinations

- **Fine-Tuning:** When you fine-tune a model, you modify its mathematical weights. The model "memorizes" patterns, but has no source of truth to consult. If you ask it something that was not exactly in the training data, the model will hallucinate (invent information) trying to sound convincing, and you have no way to trace where that answer came from.
- **RAG:** The model memorizes nothing. RAG searches a database for the exact document (a PDF, an article, a manual), gives it to the LLM in the prompt, and says: "Answer based only on this." If the LLM answers, you can ask it to show the source or the exact citation.

### 2. Costly and slow data updates

- **Fine-Tuning:** The model's knowledge is frozen at the moment training ends. If your data changes (for example, you update product prices, change an internal policy, or there is a new law), you have to pay again and retrain the model from scratch with the new data.
- **RAG / Plugins:** The information is dynamic. If you change a price in your database or a paragraph in a PDF, the RAG vector database or the Plugin detects it instantly. The base model stays the same, but it always accesses real-time information.

### 3. Does not respect user permissions (Data governance)

- **Fine-Tuning:** If you train a model with all of your company's information, the knowledge gets mixed into its "brain." You cannot tell the model: "If a regular employee asks you, don't show them the finance data, but if the CFO asks, yes." Once trained, anyone with access to the model can extract any information through prompt engineering.
- **RAG / Plugins:** Very easy. You can put a filter before the data search: "If the current user is not an Administrator, don't search the Finance folder." The model will only see what the file system allows it to retrieve.

### 4. Hidden costs (Training and Hosting)

- **Fine-Tuning:** You not only pay for the training process. On platforms like OpenAI, the cost per million tokens (both input and output) of a fine-tuned model is significantly higher (sometimes double or triple) than that of the base model you started from.
- **RAG / Plugins:** You use the standard base model (which is always the cheapest and most optimized). The only extra cost is the storage of the vector database, which is usually ridiculously cheap.

### 5. Cannot perform actions in the real world

- **Fine-Tuning:** The model only learns to speak or format in a specific way. It cannot check the weather, send an email, or look up a customer's balance in your CRM.
- **Skills / Plugins:** You give the model "hands." Through Function Calling, the model can decide autonomously: "To answer this, I need to run the Skill `query_inventory(product_id=123)`," bringing fresh data or executing actions on external systems.

### Industry golden rule

- **Use Fine-Tuning** to change the HOW the model speaks (tone of voice, writing style, following strict JSON format, learning a rare programming language).
- **Use RAG and Plugins** to change the WHAT the model knows (your company's data, documents, connecting to external systems, information that changes day by day).
