# SDR-Based FM Broadcast Compliance Monitoring System

Technical specification for an SDR-based FM broadcast compliance monitoring system covering architecture, measurement framework, and processing algorithms. This is a **LaTeX document project** — no executable software code is produced.

The specification is **hardware-agnostic**: the design references a generic "SDR device" / "baseline SDR platform" with no vendor product context (8-bit ADC class baseline characterization: ~7 ENOB, ~42 dB dynamic range, ±20 ppm crystal, 20 MS/s).

## Quick path

1. Compile: `latexmk -g -outdir=report report/RAG-informed-SpectrumSensing.tex`
2. Clean: `latexmk -c -outdir=report report/RAG-informed-SpectrumSensing.tex`
3. Output: `report/RAG-informed-SpectrumSensing.pdf`

> Always pass `-outdir=report`: without it latexmk writes the PDF and aux files to the project root instead of next to the source, leaving a stale PDF and untracked junk.

## Building the document

### Essential commands

```bash
# Standard compile + clean cycle
latexmk -g -outdir=report report/RAG-informed-SpectrumSensing.tex && latexmk -c -outdir=report report/RAG-informed-SpectrumSensing.tex

# Full reset (clean everything, rebuild from scratch)
latexmk -gg -outdir=report report/RAG-informed-SpectrumSensing.tex

# Debug compile with full log output
latexmk -g -verbose -outdir=report report/RAG-informed-SpectrumSensing.tex
```

### latexmk cheat sheet

| Command | What it does |
|---------|--------------|
| `latexmk -g file.tex` | Force rebuild, produce PDF |
| `latexmk -c` | Clean aux files (.aux, .log, .out, .toc) |
| `latexmk -C` | Clean everything including PDF |
| `latexmk -gg file.tex` | Clean + rebuild regardless of timestamps |
| `latexmk -outdir=report file.tex` | Write PDF + aux to `report/` — **required** for this project |
| `latexmk -pvc -view=pdf file.tex` | Live preview — recompiles on save |
| `latexmk -g -jobname=final file.tex` | Compile to `final.pdf` instead of default name |
| `latexmk -g -verbose file.tex` | Full log output for debugging |

Full flag reference: `latexmk --help | head -80`

## What this document specifies

| Area | Coverage |
|------|----------|
| Regulatory basis | FCC Part 73, ANE Resolution 105, ITU-R BS.412, BS.450-4, SM.2152, ISO/IEC 17025 |
| Target platform | Vendor-agnostic SDR device (8-bit ADC baseline, 87.5–108 MHz VHF-II observation; 88–108 MHz regulated FM band) |
| DSP pipeline | 6 stages: Acquisition → Preprocessing → Spectral Estimation → Channel Detection → Carrier Estimation → Confidence Scoring |
| Compliance measurands | Frequency error, received power, field strength, occupied bandwidth, ACLR, peak deviation, channel occupancy |
| Capability classes | Compliance-Grade, Screening-Grade, Conditional Compliance-Grade, Unsupported |
| Decision frameworks | Simple threshold, Guard-band (ISO 14253-1), Shared-risk |
| Uncertainty | GUM-compliant Type A/B evaluation, combined/expanded uncertainty, periodic re-evaluation |
| Array coordination | Inter-node calibration, timing alignment, fusion rules, degraded-mode operation |

## Searching reference documents (local-rag)

Eight reference PDFs are ingested into a vector database for hybrid keyword + semantic search. The RAG index lives at `/home/javastral/RAG-documents/` (outside this repo)

### Sync new PDFs

Place new PDFs in `/home/javastral/RAG-documents/` then run `local-rag_sync_start` with the file or directory path. Check status with `local-rag_sync_status`.

### Check index health

`local-rag_status` returns: document count, chunk count, memory usage, search mode, FTS index state.

