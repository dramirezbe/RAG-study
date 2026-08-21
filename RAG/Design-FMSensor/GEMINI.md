# GEMINI.md — RAG/Design-FMSensor

## Project Overview

LaTeX-based technical specification for an **SDR-agnostic** FM broadcast compliance monitoring system (VHF-II, 87.5–108 MHz). This is a **document project**, not a software codebase.

The specification is hardware-agnostic by design: every reference is to a generic "SDR device" / "baseline SDR platform", never to a specific product. Baseline numeric characterizations (8-bit ADC, ~7 ENOB, ~42 dB dynamic range, ±20 ppm crystal, 20 MS/s class) are preserved as baseline platform values for capability classification.

- **Primary document**: `report/RAG-informed-SpectrumSensing.tex` (~1280 lines, unnumbered Introduction + 10 numbered sections) — the current, RAG-verified, hardware-agnostic specification. Tracked PDF: `report/RAG-informed-SpectrumSensing.pdf`.
- **Legacy copy**: `template/03SpectrumSensingFM0-copy.tex` — pre-refactor version (HackRF One specific). **Do not edit** (historical reference only).

---

## Build Instructions (LaTeX)

```bash
# Standard compile (force rebuild into report directory)
latexmk -g -outdir=report report/RAG-informed-SpectrumSensing.tex

# Clean auxiliary build files (always clean after building)
latexmk -c -outdir=report report/RAG-informed-SpectrumSensing.tex

# Combined compile + clean cycle
latexmk -g -outdir=report report/RAG-informed-SpectrumSensing.tex && latexmk -c -outdir=report report/RAG-informed-SpectrumSensing.tex

# Full reset and rebuild
latexmk -gg -outdir=report report/RAG-informed-SpectrumSensing.tex
```

> [!CRITICAL]
> Always pass `-outdir=report`. Without it, `latexmk` writes output and aux files to the current directory, resulting in stale PDFs and untracked root clutter.

---

## `mcp-local-rag` Document Search & Verification

The primary document was constructed and verified using the **`mcp-local-rag`** MCP server.

### Index Location & Status
- **Vector Index Path**: `/home/javastral/RAG-documents/` (external directory).
- **Index State**: **8 reference documents / 7,083 chunks** (hybrid semantic + FTS search).
- **Local PDFs (`docs-RAG-FM/` / `docs-RAG/`)**: Human reading copies only; never treat as the live index.
- Check live status with: `local-rag_status` / `status`.

### Available Ingested Standards
1. **FCC Part 73** (`47_CFR_Part_73.pdf`): §73.317 (FM transmission system requirements / emission masks), §73.1545 (Carrier frequency tolerance: ±2000 Hz for FM >10 W).
2. **ANE Colombia** (`ANE_0105_2020.pdf`, `ANE_0463_2020.pdf`, `ANE_0406_2026.pdf`): Colombian technical spectrum standards and FM broadcasting regulation.
3. **ITU-R BS.412-9** (`BS.412-9.pdf`): Planning standards for FM sound broadcasting, co-channel/adjacent-channel RF protection ratios.
4. **ITU-R BS.450-4** (`BS.450-4.pdf`): Transmission standards for FM sound broadcasting, stereophonic multiplex (MPX), pilot tone (19 kHz ± 2 Hz), maximum frequency deviation (±75 kHz).
5. **ITU-R SM.2152** (`SM.2152.pdf`): Definitions of Software Defined Radio (SDR) and Cognitive Radio Systems (CRS).
6. **ISO/IEC 17025:2017** (`ISO_IEC_17025_2017.pdf`): Metrological traceability (§6.5), measurement integrity (§6.4), decision rules and statements of conformity (§3.7, §7.8.6).

### MCP Query Tools

- **`query_documents` / `local-rag_query_documents`**:
  - `query`: Keyword + semantic phrase (e.g., `"carrier frequency tolerance FM broadcast"`, `"emission mask attenuation FCC Part 73"`).
  - `limit`: Number of results (default 10, max 20).
  - `scope`: Optional path prefix to restrict queries to a single standard (e.g., `/home/javastral/RAG-documents/47_CFR_Part_73.pdf`).
- **`read_chunk_neighbors` / `local-rag_read_chunk_neighbors`**:
  - Retrieve preceding and succeeding chunks (`filePath`, `chunkIndex`, `count`) to inspect complete regulatory clauses, tables, and mathematical formulas.

### Common Regulatory Queries

| Topic / Parameter | Query Example | Expected Reference |
|---|---|---|
| Frequency Tolerance | `carrier frequency tolerance FM broadcast` | FCC §73.1545 (±2000 Hz) / ANE 0105 |
| Occupied Bandwidth | `occupied bandwidth 99 percent FM emission mask` | 47 CFR §73.317 |
| Modulation & Pilot | `multiplex pilot tone 19 kHz frequency deviation 75 kHz` | ITU-R BS.450-4 / FCC §73.322 |
| Protection Ratios | `RF protection ratios co-channel adjacent channel` | ITU-R BS.412-9 |
| Uncertainty & GUM | `GUM uncertainty measurement Type A Type B JCGM 100` | ISO 17025 / JCGM 100:2008 |
| Conformity Decision | `decision rule guard-band ISO 14253 shared risk` | ISO 17025 §7.8.6 / ISO 14253-1 |
| SDR Definition | `Software Defined Radio digital processing reconfigurability` | ITU-R SM.2152 |

---

## Document Structure & Section Mapping

1. **Introduction** (unnumbered) — Regulatory context (FCC, ANE, ITU-R BS.412, BS.450-4, SM.2152, ISO 17025).
2. **System-Level Requirements** — Reconfigurability (SM.2152), measurement integrity (ISO 17025 §6.4/§6.5), calibration tiers.
3. **Functional Decomposition** — RF front-end, digital processing engine, data management; jurisdiction-aware band (87.5–108 vs 88–108 MHz) and raster (100 kHz ANE / 200 kHz FCC).
4. **FM Compliance Measurands** — Primary/secondary observables, Table 1 measurands, 4-class capability classification.
5. **Node-Level DSP Pipeline** — 6-stage acquisition-to-reporting pipeline.
6. **Array-Level Coordination** — Inter-node calibration, timing sync, fusion rules, degraded-mode operation (ISO 17025 §7.7).
7. **Baseline SDR Platform Assessment** — Hardware-agnostic platform limitations, upgrade paths, theoretical-vs-practical capabilities.
8. **Reference Requirements** — Per-measurand compliance-grade thresholds; jurisdiction-aware tolerances; MPX 0 dBr procedure definition.
9. **Estimation Pipeline of Measurands** — Estimator algorithmic details, raw sample formats (CF32/CS16/CS12/CS8/CU8/CS4), I/Q normalization formula.
10. **Uncertainty Budget and Reporting** — GUM (JCGM 100:2008) Type A/B evaluation, combined/expanded uncertainty.
11. **Compliance Decision Logic and Uncertainty Handling** — Simple threshold, guard-band (ISO 14253-1 + JCGM 106:2012), shared-risk rules.

---

## Refactoring Workflows (LAP1 → LAP2)

- **LAP1 (Completed)**: Evidence-anchored refactor against the RAG corpus (`PLAN_RAG_latex.md`, audit log `PLAN_CHECKPOINTS.md`).
- **LAP2 (Pending)**: Gap and contradiction hunting, redundant argument reduction (`PLAN_FEEDBACK.md`, log `PLAN_CHECKPOINTS_lap2.md`).
- Note: LAP2 edits must target `report/RAG-informed-SpectrumSensing.tex` and re-verify against `local-rag` queries.

## Recommended Skills
- `cognitive-doc-design`: Technical documentation structure and visual clarity.
- `judgment-day`: Adversarial auditing and review of document claims.
