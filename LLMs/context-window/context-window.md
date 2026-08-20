# Soluciones SOTA para Context Window en LLMs

## Contexto Largo (1M+ tokens)

**Referencias principales:**
— Google DeepMind (2024). *Gemini 1.5: Unlocking multimodal understanding across millions of tokens of context.* [arXiv:2403.05530](https://arxiv.org/abs/2403.05530).
— Liu et al. (2024). *World Model on Million-Length Video and Language with RingAttention.* [arXiv:2402.08268](https://arxiv.org/abs/2402.08268).
— Anthropic (2024). *The Claude 3 Model Family: Opus, Sonnet, Haiku.* Model Card.
— Peng et al. (2023). *YaRN: Efficient Context Window Extension of Large Language Models.* [arXiv:2309.00071](https://arxiv.org/abs/2309.00071).

**Descripción:** La carrera por extender la ventana de contexto de los LLMs ha pasado de decenas de miles a millones de tokens en menos de dos años. Los modelos SOTA actuales (Gemini 1.5 Pro, Claude 3, GPT-4 Turbo) ofrecen contextos de 128K–1M tokens, habilitando aplicaciones como análisis de repositorios completos, procesamiento de videos largos, y razonamiento sobre corpus documentales masivos. Las estrategias principales para alcanzar estas longitudes incluyen RoPE scaling ([YaRN](https://arxiv.org/abs/2309.00071), NTK-aware, Self-Extend), atención por bloques distribuida ([RingAttention](https://arxiv.org/abs/2310.01889)), y arquitecturas híbridas.

**Características principales:**

- **Capacidad máxima actual:** Gemini 1.5 Pro soporta hasta 10M tokens (en beta cerrada) y 1M tokens en disponibilidad general, con calidad sostenida en *needle-in-a-haystack* (>99% recall en 1M tokens). Claude 3 ofrece 200K tokens con recall >98%.
- **Técnica de extensión predominante:** [YaRN](https://arxiv.org/abs/2309.00071) (Yet another RoPE extensioN) y sus variantes (NTK-aware, NTK-by-parts, Dynamic YaRN) son el enfoque más adoptado para modelos basados en RoPE (Llama 2/3, Mistral, Qwen). Requieren solo 400–1000 pasos de fine-tuning.
- **Self-Extend (Jin et al., 2024):** Extensión zero-shot sin fine-tuning usando atención agrupada a nivel de *floor* — los tokens cercanos se atienden con precisión completa mientras los lejanos se agrupan. Efectivo hasta 4× la longitud de entrenamiento original.
- **Entrenamiento nativo largo (Anthropic):** Claude 3 entrena directamente en secuencias largas sin depender de interpolación post-hoc, usando técnicas propietarias de entrenamiento distribuido.
- **Limitaciones:** Aunque el recall posicional es alto, los modelos aún degradan en tareas de razonamiento multi-hop y síntesis sobre contextos largos. El costo computacional escala con la longitud (aunque [FlashAttention-3](https://arxiv.org/abs/2407.08608) reduce el overhead a O(N log N)).
- **Evaluación estándar:** *Needle-in-a-Haystack* (NIAH), *RULER* (synthetic long-context tasks), *LOFT* (Long-Context Frontiers), y *LongBench*.

**Propósito:** Permitir que los LLMs procesen, razonen y sinteticen información de documentos extremadamente largos (libros completos, bases de código, videos de horas) en una sola pasada, sin necesidad de segmentación, resumen intermedio o recuperación externa.

---

## RAG (Retrieval-Augmented Generation)

**Referencia:** Lewis et al. (2020). *Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks.* NeurIPS 2020. [arXiv:2005.11401](https://arxiv.org/abs/2005.11401).
— Gao et al. (2023). *Retrieval-Augmented Generation for Large Language Models: A Survey.* [arXiv:2312.10997](https://arxiv.org/abs/2312.10997).

**Descripción:** Paradigma que combina un LLM generativo con un sistema de recuperación externo (*retriever*). En lugar de codificar todo el conocimiento o contexto en la ventana del modelo, el sistema indexa documentos en una base de conocimiento vectorial, recupera los fragmentos más relevantes para cada consulta, y los inyecta en el prompt del LLM. Esto permite acceso a volúmenes masivos de información sin saturar la ventana de contexto.

**Características principales:**

- **Arquitectura:** Pipeline de 3 etapas: (1) *indexing* — los documentos se dividen en chunks, se codifican como embeddings y se almacenan en una base vectorial; (2) *retrieval* — ante una query, se recuperan los $k$ chunks más similares; (3) *generation* — el LLM recibe la query + chunks recuperados y genera la respuesta.
- **Modalidades de RAG:**
  - **Naive RAG:** Recuperación simple + generación directa sin procesamiento adicional.
  - **Advanced RAG:** Incluye re-ranking de chunks, query rewriting, y filtrado de relevancia pre-generación.
  - **Modular RAG:** Componentes intercambiables (retrievers híbridos, memoria, adaptadores de dominio, verificación post-hoc).
- **Complemento al contexto largo:** RAG y contexto largo no son excluyentes. Los modelos con contextos de 128K+ se benefician de RAG porque múltiples chunks caben en una sola ventana, reduciendo el número de llamadas al retriever. *LongRAG* (Jiang et al., 2024) usa chunks de 4K tokens en lugar de los típicos 100–200 tokens.
- **Graph RAG (Microsoft, 2024):** Extiende RAG con grafos de conocimiento generados por LLMs (*graph indexing*) que capturan relaciones entre entidades y temas, permitiendo razonamiento multi-hop y síntesis temática global más allá de la similitud vectorial local.
- **Agentic RAG:** Incorpora agentes que iteran entre recuperación y generación, evalúan la suficiencia de la información, reformulan queries y deciden cuándo buscar más datos.

**Propósito:** Superar las limitaciones de conocimiento estático y ventana finita de los LLMs: acceder a información actualizada, específica de dominio o privada sin fine-tuning, manteniendo la fidelidad factual mediante la atribución de fuentes.

**Cita (arXiv):**
- **RAG original:** [arXiv:2005.11401](https://arxiv.org/abs/2005.11401)
- **Survey:** [arXiv:2312.10997](https://arxiv.org/abs/2312.10997)

---

## Fine-tuning para Extensión de Contexto

**Referencia:** Chen et al. (2023). *Extending Context Window of Large Language Models via Positional Interpolation.* (Position Interpolation, PI). [arXiv:2306.15595](https://arxiv.org/abs/2306.15595).
— Peng et al. (2023). *YaRN: Efficient Context Window Extension of Large Language Models.* [arXiv:2309.00071](https://arxiv.org/abs/2309.00071).
— Xiong et al. (2023). *Effective Long-Context Scaling of Foundation Models.* (Llama 2 Long). [arXiv:2309.16039](https://arxiv.org/abs/2309.16039).
— Fu et al. (2024). *Data Engineering for Scaling Language Models to 128K Context.* (Long Data Curriculum). [arXiv:2402.10171](https://arxiv.org/abs/2402.10171).

**Descripción:** El fine-tuning para contexto largo busca adaptar un LLM pre-entrenado (típicamente en 2K–8K tokens) a ventanas de 32K–128K+ tokens mediante modificaciones a los embeddings posicionales y entrenamiento continuo con datos de secuencias largas. Las técnicas se dividen en: (1) *positional interpolation* (ajuste continuo o por tramos de las rotaciones RoPE), (2) *data engineering* (mezcla y curriculum de datos largos/cortos), y (3) *training efficiency* (técnicas que reducen el costo del fine-tuning).

**Características principales:**

- **Position Interpolation (PI, Meta 2023):** Reduce linealmente las frecuencias rotacionales para que las posiciones extendidas quepan en el rango de entrenamiento original. Simple pero degrada distancias cortas. [arXiv:2306.15595](https://arxiv.org/abs/2306.15595)
- **YaRN (Nous Research, 2023):** Combina interpolación NTK-aware (frecuencias altas mantienen resolución, bajas se comprimen), partición *by-parts* (distintos factores según la longitud de onda), y *dynamic scaling* en inferencia (ajuste de temperatura de softmax). Gold standard: 128× extensión con ~400–1000 pasos. [arXiv:2309.00071](https://arxiv.org/abs/2309.00071)
- **Llama 2 Long (Meta, 2023):** Fine-tuning continuo con RoPE ajustado por NTK + mezcla de datos 90% cortos / 10% largos para preservar capacidades en short-context. Efectivo hasta 32K tokens. [arXiv:2309.16039](https://arxiv.org/abs/2309.16039)
- **Long Data Curriculum (Fu et al., 2024):** Muestra que la clave no está solo en la técnica posicional sino en la calidad y progresión de los datos de fine-tuning: escalar gradualmente la longitud (curriculum) y mantener diversidad de dominios evita el colapso de PPL en secuencias largas. [arXiv:2402.10171](https://arxiv.org/abs/2402.10171)
- **SFT + DPO para long-context:** El fine-tuning supervisado (SFT) se complementa con DPO (Direct Preference Optimization) usando pares de preferencia donde la respuesta correcta requiere atender todo el contexto, entrenando al modelo a *usar* efectivamente la ventana extendida, no solo a no colapsar.
- **LoRA / QLoRA:** Adaptadores de bajo rango permiten fine-tuning de contexto largo con memoria de GPU drásticamente reducida (ej. 1×A100 para Llama-2-7B a 32K con QLoRA vs 8×A100 para full fine-tuning).

**Propósito:** Adaptar modelos pre-entrenados existentes a contextos extendidos preservando sus capacidades previas, minimizando el cómputo necesario y asegurando que el modelo efectivamente *utilice* la ventana ampliada en tareas reales (no solo que no colapse en perplejidad).

**Cita (arXiv):**
- **Position Interpolation:** [arXiv:2306.15595](https://arxiv.org/abs/2306.15595)
- **YaRN:** [arXiv:2309.00071](https://arxiv.org/abs/2309.00071)
- **Llama 2 Long:** [arXiv:2309.16039](https://arxiv.org/abs/2309.16039)
- **Long Data Curriculum:** [arXiv:2402.10171](https://arxiv.org/abs/2402.10171)

---

## Memory Layers

**Referencia:** Burtsev et al. (2020). *Memory Transformer.* [arXiv:2006.11527](https://arxiv.org/abs/2006.11527).
— Wang et al. (2024). *MemGPT: Towards LLMs as Operating Systems.* [arXiv:2310.08560](https://arxiv.org/abs/2310.08560).
— Munkhdalai et al. (2024). *Leave No Context Behind: Efficient Infinite Context Transformers with Infini-attention.* (Google). [arXiv:2404.07143](https://arxiv.org/abs/2404.07143).
— Liu et al. (2024). *Augmenting Language Models with Long-Term Memory.* (LongMem). NeurIPS 2023. [arXiv:2306.07174](https://arxiv.org/abs/2306.07174).
— Gu & Dao (2023). *Mamba: Linear-Time Sequence Modeling with Selective State Spaces.* [arXiv:2312.00752](https://arxiv.org/abs/2312.00752).

**Descripción:** Las memory layers incorporan componentes de memoria persistente en la arquitectura del LLM que permiten almacenar y recuperar información más allá de la ventana de atención inmediata. A diferencia del KV-cache (que solo cubre el contexto activo de la sesión), estas memorias persisten a través de sesiones, documentos y tareas, habilitando un verdadero *long-term memory* en el modelo.

**Características principales:**

- **Memory Transformer (2020):** Agrega tokens de memoria persistente concatenados a la secuencia de entrada que actúan como registro de lectura/escritura a lo largo del contexto. Estos tokens memorizan información de segmentos previos y la hacen disponible a segmentos futuros, extendiendo el contexto efectivo más allá de la ventana de atención. [arXiv:2006.11527](https://arxiv.org/abs/2006.11527)
- **Infini-attention (Google, 2024):** Combina atención local (ventana deslizante) con memoria compresiva global usando *linear attention*: cada segmento nuevo actualiza un estado compresivo fijo (matriz de memoria + normalización), que luego se consulta junto con la atención local. Complejidad $O(N)$ en memoria, permite contexto "infinito" en una sola GPU. [arXiv:2404.07143](https://arxiv.org/abs/2404.07143)
- **MemGPT (UC Berkeley, 2024):** Implementa memoria virtual inspirada en sistemas operativos: el modelo gestiona explícitamente un *main context* (ventana activa) y un *external context* (almacenamiento persistente), moviendo datos entre ambos mediante llamadas a función explícitas (write/read/search). El modelo *aprende* cuándo almacenar y recuperar de su memoria externa. [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- **LongMem (NeurIPS 2023):** Arquitectura con un banco de memoria externa (key-value store) acoplado vía *side-network* al modelo congelado. La memoria se lee mediante atención cruzada entre el estado actual del decoder y las keys almacenadas, y se actualiza incrementalmente. Permite atender a información de contextos arbitrariamente largos (hasta 65K tokens demostrados) sin aumentar el costo de la atención primaria. [arXiv:2306.07174](https://arxiv.org/abs/2306.07174)
- **State Space Models (Mamba, 2023):** Reformulan la memoria como estado recurrente selectivo con complejidad $O(N)$. Aunque no son estrictamente *memory layers*, incorporan compresión secuencial implícita que funciona como memoria compresiva *built-in*, extrapolando hasta 1M tokens sin degradación de perplejidad. [arXiv:2312.00752](https://arxiv.org/abs/2312.00752)
- **Limitación principal:** La calidad de la memoria compresiva es inferior a la atención exacta para tareas que requieren *recall* preciso de detalles finos en contextos lejanos. La investigación actual busca cerrar esta brecha con mecanismos híbridos (atención + compresión).

**Propósito:** Romper la barrera del contexto cuadrático añadiendo memoria persistente al modelo, permitiéndole mantener y acceder a información a lo largo de sesiones y documentos sin tener que re-procesar todo el historial en cada paso de inferencia.

**Cita (arXiv):**
- **Infini-attention:** [arXiv:2404.07143](https://arxiv.org/abs/2404.07143)
- **MemGPT:** [arXiv:2310.08560](https://arxiv.org/abs/2310.08560)
- **LongMem:** [arXiv:2306.07174](https://arxiv.org/abs/2306.07174)

---

## Vector Stores

**Referencia:** Johnson et al. (2019). *Billion-scale similarity search with GPUs.* (FAISS, Meta). [arXiv:1702.08734](https://arxiv.org/abs/1702.08734).
— Douze et al. (2024). *The Faiss library.* (FAISS v2). [arXiv:2401.08281](https://arxiv.org/abs/2401.08281).
— Wang et al. (2021). *Milvus: A Purpose-Built Vector Data Management System.* (Milvus). SIGMOD 2021.
— Jin et al. (2024). *ColBERT: Efficient and Effective Passage Search via Contextualized Late Interaction over BERT.* (ColBERT, multi-vector). [arXiv:2004.12832](https://arxiv.org/abs/2004.12832).
— Günther et al. (2024). *Jina Embeddings v3.* (Jina AI, late-chunking).

**Descripción:** Las vector stores (bases de datos vectoriales) son sistemas especializados en almacenar y buscar sobre representaciones vectoriales de alta dimensionalidad (*embeddings*). Constituyen el componente de *indexing y retrieval* en pipelines RAG y son esenciales para escalar el acceso a información más allá de lo que cabe en la ventana de contexto. Permiten indexar millones o billones de documentos y recuperar los más relevantes en milisegundos mediante búsqueda aproximada de vecinos más cercanos (ANN).

**Características principales:**

- **Algoritmos de indexación ANNS:**
  - **HNSW (Hierarchical Navigable Small World):** Estructura de grafo multicapa con complejidad de búsqueda $O(\log N)$. Balance óptimo entre velocidad y recall para la mayoría de casos de uso (usado por Milvus, Qdrant, Weaviate).
  - **IVF-PQ (Inverted File + Product Quantization):** Compresión de vectores para reducir memoria. [FAISS](https://arxiv.org/abs/2401.08281) implementa variantes con GPUs que alcanzan búsquedas sub-milisegundo en billones de vectores.
  - **DiskANN (Microsoft):** Indexación con soporte SSD para almacenamiento masivo con bajo costo y latencia aceptable.
- **Estrategias de chunking:** El particionado de documentos define la unidad de recuperación:
  - **Fixed-size chunking:** Tamaño fijo con overlap (típicamente 256–512 tokens con 10–20% de solapamiento). Simple y robusto.
  - **Semantic chunking:** División por límites semánticos (párrafos, secciones) usando embeddings de oraciones adyacentes y umbrales de similitud.
  - **Late chunking (Jina AI, 2024):** El embedding se computa sobre el documento completo (contexto global) y luego se segmenta; las representaciones resultantes mantienen información contextual que se perdería con chunking ingenuo.
  - **ColBERT-style multi-vector:** Cada token del chunk tiene su propio embedding; la similitud query-documento se computa como *late interaction* (MaxSim) entre vectores individuales, capturando relevancia a nivel de token. [arXiv:2004.12832](https://arxiv.org/abs/2004.12832)
- **Vector stores populares:**
  - **FAISS (Meta):** Librería C++/Python de más bajo nivel, máxima eficiencia y control sobre índices. No es una base de datos completa. [arXiv:2401.08281](https://arxiv.org/abs/2401.08281)
  - **Milvus:** Base de datos vectorial distribuida cloud-native con soporte para escalado horizontal, múltiples tipos de índices, filtrado por metadatos e integración con pipelines ETL.
  - **Qdrant:** Escrito en Rust, alto rendimiento, API REST/gRPC rica, soporte para filtrado de payload y búsqueda híbrida (densa + sparse/BM25).
  - **Weaviate:** Integración nativa con LLMs vía módulos (generative search), soporte vector + keyword híbrido, y esquema GraphQL.
- **Estrategias de retrieval:**
  - **Dense retrieval:** Embeddings densos (ej. OpenAI text-embedding-3, Cohere Embed v3, Jina Embeddings v3) para recuperación semántica. Captura significado pero puede fallar en matching exacto (nombres, códigos).
  - **Sparse/hybrid retrieval:** Combinación de BM25 (keyword matching exacto) con embeddings densos para cubrir tanto queries semánticas como terminológicas.
  - **Multi-stage retrieval:** Embedding → coarse retrieval (top-K grande, ej. 100) → re-ranker cross-encoder (modelo más preciso pero costoso) → top-K final (ej. 5). Mejora significativamente la precisión.
  - **Multi-representation indexing (ColBERT):** Documentos indexados como múltiples vectores por token con búsqueda MaxSim; más preciso que single-vector pero más costoso en almacenamiento. [arXiv:2004.12832](https://arxiv.org/abs/2004.12832)

**Propósito:** Proveer la infraestructura de recuperación semántica que permite a los LLMs acceder a volúmenes masivos de conocimiento externo con latencia de milisegundos, constituyendo la alternativa más madura y ampliamente adoptada para superar la limitación de la ventana de contexto.

**Cita (arXiv):**
- **FAISS:** [arXiv:2401.08281](https://arxiv.org/abs/2401.08281)
- **ColBERT:** [arXiv:2004.12832](https://arxiv.org/abs/2004.12832)

---

## Comparativa: Fine-tuning vs RAG vs Plugins

Es muy común pensar que el fine-tuning es la solución definitiva para que un LLM "aprenda" sobre tu negocio o datos privados, pero la realidad es que casi siempre es la herramienta equivocada para ese propósito. Para inyectar conocimiento o darle superpoderes a un modelo, metodologías como RAG (Generación Aumentada por Recuperación) o el uso de Skills/Plugins (herramientas/llamadas a funciones) suelen ser mucho más eficientes.

### 1. El problema de la "Caja Negra" y las alucinaciones

- **Fine-Tuning:** Cuando ajustas un modelo, modificas sus pesos matemáticos. El modelo "memoriza" patrones, pero no tiene una fuente de verdad a la que consultar. Si le preguntas algo que no venía exactamente en el entrenamiento, el modelo va a alucinar (inventar información) intentando sonar convincente, y no tienes forma de rastrear de dónde sacó esa respuesta.
- **RAG:** El modelo no memoriza nada. RAG busca en una base de datos el documento exacto (un PDF, un artículo, un manual), se lo da al LLM en el prompt y le dice: "Responde basándote solo en esto". Si el LLM responde, puedes pedirle que te muestre la fuente o la cita exacta.

### 2. Actualización de datos costosa y lenta

- **Fine-Tuning:** El conocimiento del modelo queda congelado en el momento en que termina el entrenamiento. Si tus datos cambian (por ejemplo, actualizas los precios de tus productos, cambias una política interna o hay una nueva ley), tienes que volver a pagar y volver a entrenar el modelo desde cero con los datos nuevos.
- **RAG / Plugins:** La información es dinámica. Si cambias un precio en tu base de datos o un párrafo en un PDF, la base de datos vectorial de RAG o el Plugin lo detectan al instante. El modelo base sigue siendo el mismo, pero siempre accede a la información en tiempo real.

### 3. No respeta permisos de usuario (Gobernanza de datos)

- **Fine-Tuning:** Si entrenas a un modelo con toda la información de tu empresa, el conocimiento se mezcla en su "cerebro". No puedes decirle al modelo: "Si te pregunta un empleado común, no le muestres los datos de finanzas, pero si pregunta el CFO, sí". Una vez entrenado, cualquiera que tenga acceso al modelo puede extraer cualquier información mediante prompt engineering.
- **RAG / Plugins:** Es facilísimo. Puedes poner un filtro antes de la búsqueda de datos: "Si el usuario actual no es Administrador, no busques en la carpeta de Finanzas". El modelo solo verá lo que el sistema de archivos le permita recuperar.

### 4. Costos ocultos (Entrenamiento y Hospedaje)

- **Fine-Tuning:** No solo pagas por el proceso de entrenamiento. En plataformas como OpenAI, el costo por cada millón de tokens (tanto de entrada como de salida) de un modelo ajustado es significativamente más alto (a veces el doble o triple) que el del modelo base del que partiste.
- **RAG / Plugins:** Usas el modelo base estándar (que siempre es el más barato y optimizado). El único costo extra es el almacenamiento de la base de datos vectorial, que suele ser ridículamente barato.

### 5. No puede realizar acciones en el mundo real

- **Fine-Tuning:** El modelo solo aprende a hablar o formatear de una manera específica. No puede consultar el clima, no puede enviar un correo, ni puede revisar el saldo de un cliente en tu CRM.
- **Skills / Plugins:** Le das "manos" al modelo. Mediante Function Calling (Llamada a funciones), el modelo puede decidir de manera autónoma: "Para responder esto, necesito ejecutar la Skill `consultar_inventario(id_producto=123)`", trayendo datos frescos o ejecutando acciones en sistemas externos.

### Regla de oro de la industria

- **Usa Fine-Tuning** para cambiar el *CÓMO* habla el modelo (tono de voz, estilo de escritura, seguir un formato JSON estricto, aprender un lenguaje de programación rarísimo).
- **Usa RAG y Plugins** para cambiar el *QUÉ* sabe el modelo (datos de tu empresa, documentos, conectar con sistemas externos, información que cambia día a día).
