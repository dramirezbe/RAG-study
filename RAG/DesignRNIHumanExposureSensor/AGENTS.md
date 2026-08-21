# AGENTS.md — DesignRNIHumanExposureSensor

## Project Overview

LaTeX-based hardware selection and normative design justification for an **SDR-based Non-Ionizing Radiation (RNI) sensor** targeting human exposure assessment in enclosed environments with high IoT device density. This is a **document project** (LaTeX + references.bib), not a software codebase.

The project is a joint research proposal between Universidad Nacional de Colombia (Manizales) and Universidad de Caldas, funded by the 2024 joint call. The sensor monitors electric field exposure levels (700 MHz -- 3.5 GHz) in enclosed spaces with dense IoT deployments, using SDR hardware, machine learning thresholding, and Kriging-based spatial exposure mapping.

- **Primary document**: `report/RNISensor.tex` — Hardware Architecture and Component Selection Specification. Currently 8 sections, only Section 4 (Antenna/Isotropic E-Field Probe Subsystem) has content; remaining sections are title-only placeholders.
- **Compiled PDF**: `report/RNISensor.pdf` (tracked, 6 pages as of 2026-08-21)
- **Bibliography**: `report/references.bib` — 9 entries (8 normative + 1 Kriging paper)

## Build Instructions

```bash
# Compile from the project area directory
latexmk -g -outdir=report report/RNISensor.tex

# Clean auxiliary files
latexmk -c -outdir=report report/RNISensor.tex
```

**CRITICAL**: always pass `-outdir=report` and run from `RAG/DesignRNIHumanExposureSensor/`. Without `-outdir=report`, latexmk writes the PDF and aux files to the current directory, leaving stale artifacts.

## local-rag (Document Search)

Eight normative PDFs are ingested into a vector database for hybrid keyword + semantic search. The RAG index lives at `/home/javastral/RAG-documents/` (outside this repo) and holds **3101 chunks / 8 documents** (verified live 2026-08-21). The `docs-RAG-RNI/` folder in this project is **not** part of the RAG index — it is a local reading copy for humans only.

### Querying Documents

Use `local-rag_query_documents` to search across all ingested reference PDFs.

**Example queries:**

| Query topic | What it finds |
|-------------|---------------|
| `isotropic triaxial probe measurement` | K.61 §8.1.3.3, K.100, K.113 — probe requirements |
| `human exposure limits RF electromagnetic fields` | ICNIRP 2020, K.91 — reference levels and basic restrictions |
| `measurement procedure compliance base station` | K.100 §6, K.61 — assessment procedures |
| `Kriging interpolation indoor exposure map` | K.113, K.91, K.61 — spatial interpolation for exposure maps |
| `límites de exposición campos electromagnéticos Colombia` | ANE 773/2023 — Colombian regulation |

### Query Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `query` | (required) | Search string — preserve specific terms, add context for vague queries |
| `limit` | 10 | Max results (range 1--20). Lower = higher precision, higher = more recall |
| `scope` | (none) | Absolute path prefix to restrict results |

### Index Status

Check current index state with `local-rag_status`:
- `documentCount`: 8
- `chunkCount`: 3101
- `memoryUsage`: ~68 MB
- `searchMode`: hybrid (keyword + semantic)
- `ftsIndexEnabled`: true

## Project Structure

- `report/` — **PRIMARY document** (LaTeX source + compiled PDF + bibliography)
  - `RNISensor.tex` — Primary document (8 sections, only §4 has content)
  - `RNISensor.pdf` — Compiled output (tracked)
  - `references.bib` — Bibliography (9 entries: 8 normative + Kriging)
- `context/` — Project scope, IoT services, and support references
  - `PROJECT_SCOPE.md` — Full project scope: objectives, methodology, schedule, 71 references
  - `LIST_IOT_SERVICES.md` — Table of wireless IoT protocols and frequency bands
  - `supports/measure-electric-field-exposure-indoor.md` — Martínez-González 2022 Kriging paper (text)
  - `supports/measure-electric-field-exposure-indoor.pdf` — Same paper (PDF)
  - `supports/Requerimiento-IoT-Ucaldas.pdf` — Original Spanish proposal (U. de Caldas)
- `docs-RAG-RNI/` — 8 normative PDFs (local reading copies only — NOT the RAG index)
- `template/` — (reserved for future use)

## Document Sections

| # | Section | Status | Key references |
|---|---------|--------|----------------|
| 1 | Hardware Design Objectives and Scope | Placeholder | — |
| 2 | Normative Sizing Requirements for Hardware | Placeholder | — |
| 3 | SDR Transceiver Core Selection and Evaluation | Placeholder | — |
| 4 | **Antenna and Isotropic E-Field Sensor Probe Subsystem** | **Content** | K.61, K.91, K.100, K.113, ICNIRP 2020, ANE 773, Martínez-González 2022 |
| 5 | Edge Computing and Processing Unit Selection | Placeholder | — |
| 6 | RF Front-End Conditioning and Circuit Protection | Placeholder | — |
| 7 | Timing, Geolocation, and Power Subsystem | Placeholder | — |
| 8 | Hardware Bill of Materials (BOM) and Compliance Matrix | Placeholder | — |

### Section 4 Key Design Decisions

- **Triaxial multiplexer architecture**: single SDR receiver + RF switch cycling through 3 orthogonal axes. Justified by: (a) ITU-T K.113 says "preferably" simultaneous (informative, not mandatory); (b) K.100 explicitly permits single-axis/sequential with post-processing; (c) 6-minute ICNIRP averaging window dominates uncertainty — 1-s mux offset = 0.28% of window.
- **Narda EF0691 as reference probe**: single-channel muxed probe (not 3 simultaneous ADCs), ±1 dB isotropic response, used in the Kriging paper for compliance-grade indoor mapping.

## Normative References (references.bib)

| # | Key | Document | Coverage |
|---|-----|----------|----------|
| 1 | `mintic_dec1370_2018` | Decreto 1370 de 2018 | Colombian EMF exposure limits framework |
| 2 | `ane_res773_2023` | Resolución 773 de 2023 | ANE adoption of ICNIRP/ITU-T limits, measurement methodology |
| 3 | `icnirp2020` | ICNIRP Guidelines (2020) | International RF-EMF exposure limits (100 kHz -- 300 GHz) |
| 4 | `itut_k52` | ITU-T K.52 (08/2024) | Compliance assessment framework, zoning criteria |
| 5 | `itut_k61` | ITU-T K.61 (10/2025) | Measurement and numerical prediction for compliance |
| 6 | `itut_k91` | ITU-T K.91 (01/2024) | Assessment, evaluation, and monitoring of RF-EMF exposure |
| 7 | `itut_k100` | ITU-T K.100 (08/2024) | In-situ measurement procedures for base stations |
| 8 | `itut_k113` | ITU-T K.113 (07/2025) | RF-EMF exposure map generation, spatial interpolation |
| 9 | `martinezgonzalez_2022` | Martínez-González et al. (2022) | Indoor Kriging exposure mapping, ELSP point reduction |

## Skills to Load

- `cognitive-doc-design` — For documentation/guide writing tasks
- `judgment-day` — For adversarial review of document content
