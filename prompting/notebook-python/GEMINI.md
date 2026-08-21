# GEMINI.md — prompting/notebook-python

## Project Overview

Jupyter notebook experiments and tutorials for prompt engineering, comparative model evaluations, and prompt pattern validation.

---

## Environment & Execution

Run notebooks with the local virtual environment at `./venv/`:

```bash
# Execute notebook top-to-bottom
./venv/bin/jupyter execute NOTEBOOK.ipynb

# Run notebook
./venv/bin/jupyter run NOTEBOOK.ipynb
```

---

## Notebook Development & Auditing Rules

### 1. Form & Structure Audit
Every `.ipynb` must satisfy:
- **Metadata**: Valid `nbformat: 4`, `nbformat_minor: 5`, valid `kernelspec` and `language_info`.
- **Cell IDs**: Unique `id` string on every cell.
- **Code Cells**: `execution_count: null` and `outputs: []` if unexecuted.
- **Valid JSON**: Clean JSON structure without syntax errors or trailing commas.

### 2. Code Quality & Execution Audit
Before finalizing any notebook modification:
- Run the full notebook top-to-bottom without errors.
- Ensure all dependencies and imports are positioned at the top of the notebook.
- **Zero hardcoded secrets**: Always use environment variables or clear placeholder markers.
- **Tidy outputs**: Truncate massive tables or debug dumps into concise visual summaries.

---

## Skills & Tooling

- Use the `jupyter-notebook-editor` skill for granular, per-cell inspection and modification of `.ipynb` files.
- Use `excalidraw-diagram` when generating visual flowcharts of prompt pipelines.
