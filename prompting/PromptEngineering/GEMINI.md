# GEMINI.md — prompting/PromptEngineering

## Project Overview

Prompt engineering experiments, tutorials, and document preparation pipelines centered on **MarkItDown** (`microsoft/markitdown`) for multi-format document conversion (PDF, DOCX, PPTX, XLSX, HTML, images, audio, CSV/JSON/XML) to LLM-ready Markdown.

---

## Environment Configuration

- **Virtual Environment**: `venv-markitdown/` in this directory.
- **Python Version**: Python 3.12 with `markitdown[all]`.
- **Activation**:
  ```bash
  source venv-markitdown/bin/activate
  ```
  *(Or execute binaries directly via `venv-markitdown/bin/python` / `venv-markitdown/bin/markitdown`)*.

> [!CAUTION]
> The virtual environment was created with `uv` and has **no pip**.
> - Always use `uv pip`: `uv pip install --python venv-markitdown/bin/python 'markitdown[all]'`
> - Never run `pip install` directly.

---

## CLI & Python Usage

### CLI Commands
```bash
# Convert file to stdout
venv-markitdown/bin/markitdown document.pdf

# Convert file and save output
venv-markitdown/bin/markitdown document.pdf -o document.md

# Convert piped stdin content
cat document.pdf | venv-markitdown/bin/markitdown
```

### Python API
```python
from markitdown import MarkItDown

md = MarkItDown(enable_plugins=False)
result = md.convert("document.pdf")
print(result.text_content)
```

---

## RAG & `mcp-local-rag` Ingestion Integration

When preparing structured text or complex regulatory tables for ingestion into RAG indexes (such as `mcp-local-rag` or Chroma/LanceDB):

1. Use `markitdown` to generate high-fidelity Markdown representations.
2. Ingest into the `local-rag` MCP index using `ingest_file` or `ingest_data` to ensure headers and code blocks are preserved.
3. Query the ingested corpus via `local-rag_query_documents` to verify chunk boundaries and semantic relevance.
