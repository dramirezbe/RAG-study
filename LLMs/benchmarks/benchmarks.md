# Benchmarks for LLM Evaluation

## GPQA (Graduate-Level Google-Proof Q&A)

**Reference:** Rein et al. (2023). *GPQA: A Graduate-Level Google-Proof Q&A Benchmark.* [arXiv:2311.12022](https://arxiv.org/abs/2311.12022).

**Description:** Dataset of 448 multiple-choice questions written by domain experts in biology, physics, and chemistry. The questions are high quality and extremely difficult: they are designed to be "Google-proof," that is, they cannot be easily solved through internet searches.

**Main characteristics:**

- **Size:** 448 multiple-choice questions.
- **Domains:** Biology, physics, and chemistry.
- **Target level:** Graduate level (PhD).
- **Difficulty for humans:** Experts with a PhD or working toward one reach 65% accuracy (74% discounting errors identified in retrospect). Highly qualified non-expert validators reach only 34%, even with unrestricted web access for over 30 minutes on average.
- **Difficulty for AI:** GPT-4 (the strongest baseline in the original paper) reached only 39% accuracy.

**Purpose:** Evaluate the ability of language models to answer high-level scientific questions that require deep reasoning, beyond factual information retrieval. It serves as a platform for scalable oversight experiments, where humans supervise AI systems that potentially surpass human capabilities.

**Citation (arXiv):** [arXiv:2311.12022](https://arxiv.org/abs/2311.12022)

---

## SWE-bench (Software Engineering Benchmark)

**Reference:** Jimenez et al. (2023). *SWE-bench: Can Language Models Resolve Real-World GitHub Issues?* [arXiv:2310.06770](https://arxiv.org/abs/2310.06770). Accepted at ICLR 2024.

**Description:** Evaluation framework comprising 2,294 software engineering problems extracted from real GitHub issues and their corresponding pull requests, from 12 popular Python repositories. Given a codebase and an issue description, the model must edit the code to resolve it.

**Main characteristics:**

- **Size:** 2,294 instances in the full version.
- **Variants:**
  - **SWE-bench Verified:** Subset of 500 instances with human filtering to remove ambiguities.
  - **SWE-bench Lite:** Subset of 300 instances curated for more economical evaluation.
  - **SWE-bench Multilingual:** 300 tasks in 9 programming languages.
  - **SWE-bench Multimodal:** 517 instances that include visual elements.
- **Repositories:** 12 popular Python repositories (Django, Flask, matplotlib, pandas, requests, scikit-learn, seaborn, sympy, etc.).
- **Difficulty for AI:** Claude 2, the best model in the original paper, solved only 1.96% of the instances. Current models reach over 65% on SWE-bench Verified (e.g., mini-SWE-agent v2).
- **Skills evaluated:** Understanding and coordinating changes across multiple functions, classes, and files; interacting with execution environments; processing extremely long contexts; complex reasoning beyond traditional code generation.

**Purpose:** Provide a realistic, sustainable, and challenging testbed for evaluating language models on autonomous software engineering tasks. Advances on SWE-bench represent steps toward more practical, intelligent, and autonomous models.

**Website and leaderboard:** [swebench.com](https://www.swebench.com)

**Citation (arXiv):** [arXiv:2310.06770](https://arxiv.org/abs/2310.06770)
