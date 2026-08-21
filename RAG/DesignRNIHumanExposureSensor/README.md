# SDR-Based Non-Ionizing Radiation (RNI) Sensor — Hardware Selection Specification

Hardware architecture and component selection specification for an SDR-based sensor node dedicated to Non-Ionizing Radiation (RNI) human exposure assessment in enclosed environments with high IoT device density. This is a **LaTeX document project** — no executable software code is produced.

Joint research project: Universidad Nacional de Colombia (Manizales) + Universidad de Caldas, 2024 joint call.

## Quick Start

```bash
# Compile
latexmk -g -outdir=report report/RNISensor.tex

# Clean
latexmk -c -outdir=report report/RNISensor.tex

# Output
report/RNISensor.pdf
```

> Always pass `-outdir=report`. Without it, latexmk writes the PDF and aux files to the project root.

## What This Document Specifies

| Area | Coverage |
|------|----------|
| Regulatory basis | ICNIRP 2020, ITU-T K.52/K.61/K.91/K.100/K.113, ANE Res. 773, Decreto 1370 |
| Target frequency | 700 MHz -- 3.5 GHz (extended to 6 GHz for Wi-Fi 5/6 and sub-6 5G NR) |
| Measurand | Electric field strength (V/m), equivalent power density (W/m²) |
| Measurement modes | Broadband exposure integration + frequency-selective spectral decomposition |
| SDR candidates | HackRF One, RTL-SDR v4, ADALM-PLUTO, USRP B200/B205mini |
| Edge compute | NVIDIA Jetson Nano / Orin Nano, Raspberry Pi 5, embedded x86 |
| Probe architecture | Triaxial isotropic E-field probe, single-SDR multiplexed readout |
| Spatial mapping | Kriging interpolation with ELSP point reduction (70% measurement reduction) |

## Document Status

| Section | Status |
|---------|--------|
| §1 Hardware Design Objectives and Scope | Placeholder |
| §2 Normative Sizing Requirements | Placeholder |
| §3 SDR Transceiver Core Selection | Placeholder |
| **§4 Antenna and Isotropic E-Field Probe** | **Complete** |
| §5 Edge Computing and Processing Unit | Placeholder |
| §6 RF Front-End Conditioning | Placeholder |
| §7 Timing, Geolocation, and Power | Placeholder |
| §8 Hardware BOM and Compliance Matrix | Placeholder |

## Project Structure

```
DesignRNIHumanExposureSensor/
├── report/                              # PRIMARY document
│   ├── RNISensor.tex                    # LaTeX source (8 sections)
│   ├── RNISensor.pdf                    # Compiled output (tracked)
│   └── references.bib                   # Bibliography (9 entries)
├── context/                             # Project scope and references
│   ├── PROJECT_SCOPE.md                 # Full project scope (objectives, methodology, 71 refs)
│   ├── LIST_IOT_SERVICES.md             # IoT wireless protocols and frequency bands
│   └── supports/                        # Support references
│       ├── measure-electric-field-exposure-indoor.md   # Kriging paper (text)
│       ├── measure-electric-field-exposure-indoor.pdf  # Kriging paper (PDF)
│       └── Requerimiento-IoT-Ucaldas.pdf               # Original Spanish proposal
├── docs-RAG-RNI/                        # 8 normative PDFs — reading copies only
├── template/                            # (reserved)
├── AGENTS.md                            # Agent context and build instructions
└── README.md                            # This file
```

## Searching Reference Documents (local-rag)

Eight normative PDFs are ingested into a vector database at `/home/javastral/RAG-documents/` (**3101 chunks / 8 documents**, hybrid search). `docs-RAG-RNI/` is a local reading copy only — not part of the index.

| Document | Chunks | Description |
|----------|--------|-------------|
| ANE Res. 773/2023 | 856 | Colombian EMF exposure limits and measurement methodology |
| ICNIRP 2020 | 775 | International RF-EMF exposure limits (100 kHz -- 300 GHz) |
| ITU-T K.91 (01/2024) | 602 | RF-EMF exposure assessment and monitoring |
| ITU-T K.52 (08/2024) | 231 | Compliance assessment framework |
| ITU-T K.61 (10/2025) | 220 | Measurement and numerical prediction for compliance |
| ITU-T K.100 (08/2024) | 196 | In-situ base station measurement procedures |
| ITU-T K.113 (07/2025) | 185 | RF-EMF exposure map generation |
| Decreto 1370/2018 | 36 | Colombian EMF exposure limits framework |

### Query Examples

| Query | Relevant standard |
|-------|-------------------|
| `isotropic triaxial probe measurement` | K.61, K.100, K.113 |
| `human exposure limits RF electromagnetic fields` | ICNIRP 2020, K.91 |
| `base station compliance measurement procedure` | K.100, K.61 |
| `Kriging interpolation indoor exposure map` | K.113, K.91 |
| `límites de exposición campos electromagnéticos Colombia` | ANE 773 |

## Key Technical Decisions

- **Single-SDR multiplexed probe** — 1 SDR + RF switch + 3 orthogonal dipoles (cheaper than 3× SDRs; normatively admissible per K.100 "permitted" and K.113 "preferably")
- **6-minute time averaging** — ICNIRP 2020 mandates 360 s; mux switching (~1 s) = 0.28% of window, negligible vs. probe isotropy error
- **Kriging with ELSP** — up to 70% measurement point reduction without map distortion (Martínez-González 2022)
- **Narda EF0691 as reference probe** — single-channel muxed probe, ±1 dB isotropic, 100 kHz -- 6 GHz
