# Benchmarks para Evaluación de LLMs

## GPQA (Graduate-Level Google-Proof Q&A)

**Referencia:** Rein et al. (2023). *GPQA: A Graduate-Level Google-Proof Q&A Benchmark.* [arXiv:2311.12022](https://arxiv.org/abs/2311.12022).

**Descripción:** Conjunto de datos de 448 preguntas de opción múltiple escritas por expertos de dominio en biología, física y química. Las preguntas son de alta calidad y extremadamente difíciles: están diseñadas para ser "a prueba de Google" (*Google-proof*), es decir, no pueden resolverse fácilmente mediante búsquedas en internet.

**Características principales:**

- **Tamaño:** 448 preguntas de opción múltiple.
- **Dominios:** Biología, física y química.
- **Nivel objetivo:** Nivel de posgrado (PhD).
- **Dificultad para humanos:** Expertos con doctorado o en proceso de obtenerlo alcanzan un 65% de precisión (74% descartando errores identificados en retrospectiva). Validadores no expertos pero altamente calificados alcanzan solo un 34%, incluso con acceso irrestricto a la web durante más de 30 minutos en promedio.
- **Dificultad para IA:** GPT-4 (línea base más fuerte del paper original) alcanzó solo un 39% de precisión.

**Propósito:** Evaluar la capacidad de los modelos de lenguaje para responder preguntas científicas de alto nivel que requieren razonamiento profundo, más allá de la recuperación de información factual. Sirve como plataforma para experimentos de supervisión escalable (*scalable oversight*), donde se busca que humanos supervisen sistemas de IA que potencialmente superan las capacidades humanas.

**Cita (arXiv):** [arXiv:2311.12022](https://arxiv.org/abs/2311.12022)

---

## SWE-bench (Software Engineering Benchmark)

**Referencia:** Jimenez et al. (2023). *SWE-bench: Can Language Models Resolve Real-World GitHub Issues?* [arXiv:2310.06770](https://arxiv.org/abs/2310.06770). Aceptado en ICLR 2024.

**Descripción:** Marco de evaluación compuesto por 2,294 problemas de ingeniería de software extraídos de *issues* reales de GitHub y sus correspondientes *pull requests*, provenientes de 12 repositorios populares de Python. Dada una base de código y la descripción de un *issue*, el modelo debe editar el código para resolverlo.

**Características principales:**

- **Tamaño:** 2,294 instancias en la versión completa.
- **Variantes:**
  - **SWE-bench Verified:** Subconjunto de 500 instancias con filtrado humano para eliminar ambigüedades.
  - **SWE-bench Lite:** Subconjunto de 300 instancias curado para evaluación más económica.
  - **SWE-bench Multilingual:** 300 tareas en 9 lenguajes de programación.
  - **SWE-bench Multimodal:** 517 instancias que incluyen elementos visuales.
- **Repositorios:** 12 repositorios populares de Python (Django, Flask, matplotlib, pandas, requests, scikit-learn, seaborn, sympy, etc.).
- **Dificultad para IA:** Claude 2, el mejor modelo en el paper original, resolvió solo el 1.96% de las instancias. Modelos actuales alcanzan más del 65% en SWE-bench Verified (ej. mini-SWE-agent v2).
- **Habilidades evaluadas:** Comprensión y coordinación de cambios a través de múltiples funciones, clases y archivos; interacción con entornos de ejecución; procesamiento de contextos extremadamente largos; razonamiento complejo más allá de la generación de código tradicional.

**Propósito:** Proporcionar un banco de pruebas realista, sostenible y desafiante para evaluar modelos de lenguaje en tareas autónomas de ingeniería de software. Los avances en SWE-bench representan pasos hacia modelos más prácticos, inteligentes y autónomos.

**Sitio web y leaderboard:** [swebench.com](https://www.swebench.com)

**Cita (arXiv):** [arXiv:2310.06770](https://arxiv.org/abs/2310.06770)
