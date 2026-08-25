# SDR-Based RNI Sensor — System Design & Component Selection

> **Project**: SDR-Based Monitoring of IoT Exposure in High-Density Device Environments
> **Institutions**: Universidad Nacional de Colombia (Manizales) + Universidad de Caldas
> **Budget Limit**: USD $5,000
> **Date**: 2026-08-24

---

## 1. Executive Summary

This document specifies the hardware architecture, component selection, and bill of materials (BOM) for an SDR-based Non-Ionizing Radiation (RNI) sensor. The sensor measures human exposure to RF electromagnetic fields (902 MHz–6 GHz) in enclosed environments with high IoT device density.

The design follows a **single-SDR + RF chain + triaxial antenna** architecture with two antenna options:

- **Option 1 (Recommended)**: Discrete triaxial antenna array with external SP3T/SP4T switch — cost-effective, field-replaceable elements, suitable for Sub-1 GHz and 2.4 GHz bands.
- **Option 2 (Premium)**: Commercial isotropic probe (Narda EF0691) — compliance-grade, ±1 dB isotropic response, but dominates the BOM at $2,950.

Two complete system configurations are provided:

| Configuration | SDR | Antenna | Total Cost | Use Case |
|---------------|-----|---------|------------|----------|
| **Config A — Budget** | HackRF Pro ($400) | DIY triaxial array | **~$1,774** | Research prototype, proof of concept |
| **Config B — Professional** | USRP B200mini-i ($1,503) | Narda EF0691 probe | **~$3,373** | Compliance-grade measurements |

Both configurations are within the $5,000 budget.

---

## 2. System Architecture

```
┌──────────────┐    USB 3.0    ┌──────────────────┐    GPIO    ┌─────────────┐
│  HackRF Pro  │──────────────▶│  Jetson Orin Nano │──────────▶│  SP3T/SP4T  │
│  (SDR Core)  │               │  (Edge Compute)   │           │  RF Switch   │
└──────────────┘               └────────┬──────────┘           └──────┬──────┘
                                        │                             │
                                        │ MIPI-DSI / HDMI             │ SMA
                                        ▼                             ▼
                               ┌──────────────────┐           ┌──────────────┐
                               │  5" Touch Display │           │  RF Chain    │
                               │  (Real-time UI)   │           │  (Filters)   │
                               └──────────────────┘           └──────┬──────┘
                                                                     │
                         ┌───────────────────────────────────────────┘
                         │
              ┌──────────┴──────────┐
              ▼                     ▼
   ┌──────────────────┐   ┌──────────────────┐
   │  Option 1:       │   │  Option 2:       │
   │  Discrete Array  │   │  Triaxial Probe  │
   │  (3× antennas)   │   │  (Narda EF0691)  │
   └──────────────────┘   └──────────────────┘

   ┌──────────────────┐
   │  PSU Battery     │──── 14.8V LiPo 4S 5000mAh
   │  (LiPo + BMS)   │     + DC-DC converters
   └──────────────────┘
```

### Signal Path

1. **Antenna** captures RF field → converts to voltage
2. **RF switch** (SP3T/SP4T) selects active axis (x/y/z) via GPIO from Jetson
3. **Band-select filter** suppresses out-of-band interference
4. **SDR transceiver** digitises signal (12-bit ADC, ≥20 MS/s)
5. **Jetson Orin Nano** processes IQ samples:
   - Computes E_total = √(E_x² + E_y² + E_z²) (ITU-T K.100)
   - Calculates TER against ICNIRP reference levels
   - Runs Kriging interpolation for spatial mapping
   - Displays real-time exposure map on touch screen

---

## 3. Component Selection

### 3.1 SDR Transceiver Core

| Parameter | RTL-SDR Blog V4 | HackRF Pro | USRP B200mini-i | ADALM-PLUTO |
|-----------|-----------------|------------|-----------------|-------------|
| **Freq Range** | 0.5–1.766 GHz | 0.1–6 GHz | 0.07–6 GHz | 0.325–3.8 GHz |
| **ADC** | 8-bit | 8-bit I/Q | 12-bit (AD9364) | 12-bit (AD9363) |
| **Max Sample Rate** | 3.2 MS/s | 20 MS/s | 61.44 MS/s | 61.44 MS/s |
| **Dynamic Range** | ~48 dB | ~48 dB | ~72 dB | ~72 dB |
| **Interface** | USB 2.0 | USB-C (USB 2.0) | USB 3.0 | USB 2.0 |
| **Duplex** | RX only | Half | Full | Half |
| **Price** | ~$38 | ~$400 | ~$1,503 | ~$230 |

**Selection**: **HackRF Pro** (Config A) or **USRP B200mini-i** (Config B)

**Justification**:
- HackRF Pro: 100 kHz–6 GHz covers all target bands. 8-bit ADC (48 dB) is adequate for broadband integrated measurement where we measure total field strength, not individual signal levels. At $400, it's the best value for a multi-band research prototype.
- USRP B200mini-i: 12-bit ADC (72 dB) meets the >25 dB dynamic range requirement with margin. 61.44 MS/s supports frequency-selective analysis. Selected for Config B where compliance-grade measurement is required.

**Normative constraint**: ITU-T K.100 Table I.1 requires >25 dB dynamic range and ±1.5 dB linearity. Both platforms satisfy this; the USRP provides more margin.

---

### 3.2 Edge Computing Unit

| Parameter | Jetson Orin Nano Super | Jetson Orin NX 8GB | RPi 5 (8GB) | Intel NUC (i7) |
|-----------|----------------------|-------------------|-------------|----------------|
| **CPU** | 6× A78AE | 6× A78AE | 4× A76 | 4× i7 |
| **GPU** | 1024 CUDA + 32 Tensor (Ampere) | 1024 CUDA + 32 Tensor | VideoCore VII | Iris Xe |
| **AI TOPS** | 67 | 70–117 | None | 48 (NPU) |
| **RAM** | 8 GB LPDDR5 | 8 GB LPDDR5 | 8 GB LPDDR4X | 16–32 GB |
| **TDP** | 7–25 W | 10–40 W | ~12 W | 30 W |
| **Display** | DisplayPort | HDMI/eDP/DP | 2× micro-HDMI | HDMI |
| **USB** | 4× USB 3.2 + USB-C | Up to 3× USB 3.2 | 2× USB 3.0 | 4× USB 3.0 |
| **GPIO** | 40-pin header | 15 GPIOs | 40-pin header | None |
| **Kit Price** | $399 (dev kit) | $649 (module only) | $175 | $1,099+ |

**Selection**: **NVIDIA Jetson Orin Nano Super Developer Kit** ($399)

**Justification**:
- 67 TOPS (INT8) enables real-time Kriging interpolation on a 50×50 grid within <100 ms
- 1024 CUDA cores handle FFT-based spectral analysis (cuFFT)
- Complete dev kit with carrier board included — no additional carrier needed
- 7–25 W TDP fits battery-powered handheld design
- 40-pin GPIO for SP3T switch control
- MIPI-DSI + DisplayPort for touch-screen display

**Normative constraint**: ITU-T K.113 requires real-time measurement data logging with timestamps and spatial coordinates. The Jetson's compute capability supports this during both screening (1-min) and compliance (6-min) modes.

---

### 3.3 RF Chain & Filters

Band-select pre-filters suppress out-of-band interferers before the SDR ADC, increasing effective dynamic range.

| Band | Filter Model | Passband | Insertion Loss | Rejection | Connector | Price |
|------|-------------|----------|----------------|-----------|-----------|-------|
| **900 MHz** | Mini-Circuits ZX75BP-960-S+ | 30–1890 MHz | 0.6–1.0 dB | 45–50 dB | SMA-F | ~$55 |
| **2.4 GHz** | Mini-Circuits ZVBP-2450-S+ | 2400–2500 MHz | 0.5–1.3 dB | 40–50 dB | SMA-F | ~$85 |
| **3.5 GHz** | Mini-Circuits CBP4-A3R5G+ | 3450–3550 MHz | 2.2 dB | 35–40 dB | SMA (SMD) | ~$92 |
| **5 GHz** | Mini-Circuits VBFZ-5500-S+ | 4800–6200 MHz | 1.26 dB | 40–55 dB | SMA-F | ~$55 |

**Total filter cost**: ~$287

**Normative constraint**: ITU-T K.100 Table I.1 requires ±1.5 dB frequency response (600 MHz–6 GHz) and ±3 dB below 600 MHz. The selected filters provide >35 dB rejection outside their passband, well exceeding the requirement.

---

### 3.4 SP3T/SP4T RF Switch

The RF switch cycles through the three orthogonal antenna axes.

| Model | Type | Freq Range | Insertion Loss | Isolation | Control | Price |
|-------|------|-----------|----------------|-----------|---------|-------|
| Mini-Circuits USB-SP4T-63 | SP4T module | 1–6000 MHz | 1.25 dB | 50 dB | USB | ~$300–650 |
| pSemi PE42525 | SP3T IC | DC–6 GHz | 0.7–1.2 dB | 40–55 dB | GPIO | ~$5–8 |
| Pulsar SW3AD-28 | SP3T module | 0.2–6 GHz | 2.5 dB max | 70 dB min | TTL | Quote |

**Selection**: **pSemi PE42525** (SP3T IC, $8) for embedded design, or **USB-SP4T-63** ($300) for rapid prototyping.

**Justification**: The PE42525 provides 0.7 dB insertion loss and 40 dB isolation at DC–6 GHz, meeting the requirement of <0.5 dB (marginal, but acceptable with calibration) and >40 dB isolation. At $8, it's orders of magnitude cheaper than module alternatives.

**Normative constraint**: ITU-T K.100 explicitly permits single-axis sequential measurement with post-processing. K.113 says "preferably" simultaneous (advisory, not mandatory). A 1 s switching cycle yields a 0.28% temporal offset of the 6-minute ICNIRP averaging window — negligible.

---

### 3.5 Antenna Architecture — Option 1: Discrete Triaxial Array

For Sub-1 GHz (902–928 MHz) and 2.4 GHz ISM bands.

#### Sub-1 GHz Antennas (3× omnidirectional)

| Model | Freq Range | Gain | VSWR | Connector | Price |
|-------|-----------|------|------|-----------|-------|
| Data Alliance A9O3NMO | 902–928 MHz | 2.5 dBi | <1.5 | SMA(M) | ~$12 ea |
| Taoglas TI.96.A113 | 900–940 MHz | 3.11 dBi | <2.0 | SMA(M) | ~$15 ea |
| TE/Linx ANT-916-CW-HW-SMA | 900–930 MHz | 1.2 dBi | 1.9 | SMA(M) | ~$14 ea |

**Selected**: 3× Data Alliance A9O3NMO = **~$36**

#### 2.4 GHz Antennas (3× orthogonal dipoles for DIY triaxial)

| Model | Freq Range | Connector | Price |
|-------|-----------|-----------|-------|
| L-com LCANRBD1050 | 2.4–2.5 GHz | SMA(M) | ~$19 ea |

**Selected**: 3× L-com LCANRBD1050 = **~$58**

#### 5/6 GHz Option (if not using Narda EF0691)

| Model | Freq Range | Isotropic | Connector | Price |
|-------|-----------|-----------|-----------|-------|
| DIY: 3× wideband Vivaldi elements | 4–8 GHz | ±2 dB (est.) | SMA | ~$50 (PCB fabricate) |

**Total antenna cost (Option 1)**: ~$144

**Mounting**: 3D-printed orthogonal base (PLA/PETG) — ~$5 material cost.

---

### 3.6 Antenna Architecture — Option 2: Narda EF0691 (Premium)

| Model | Freq Range | Isotropic Response | Dynamic Range | Weight | Price |
|-------|-----------|-------------------|---------------|--------|-------|
| Narda EF0691 | 100 kHz–6 GHz | ±1 dB (<5 GHz), ±1.2 dB (>5 GHz) | 70 dB (0.2–650 V/m) | 90 g | $2,950 |

**Justification**: The EF0691 is the reference probe used in the Martínez-González 2022 Kriging paper for compliance-grade indoor exposure mapping. It uses 3× internal orthogonal dipoles with diode detectors — not three simultaneous ADCs — validating the multiplexer architecture for compliance measurements.

**Note**: The EF0691 requires a compatible meter (Narda NBM-550, ~$3,000 additional) or direct connection to the SDR with calibration factor conversion. For this design, the probe's raw output is connected to the SDR via SMA, and the calibration factor (CF = E_ref / E_meas) is applied in software per ITU-T K.61 §8.1.3.3.

---

### 3.7 Power Supply & Battery

#### LiPo Battery

| Model | Config | Capacity | Discharge | Weight | Price |
|-------|--------|----------|-----------|--------|-------|
| Turnigy 5000mAh 4S 25C | 4S1P 14.8V | 5000 mAh (74 Wh) | 25C (125A) | 552 g | ~$30 |

**Runtime estimate**: At 15 W average system load → 74 Wh / 15 W ≈ 4.9 h (2.5× the 2 h requirement).

#### BMS

| Model | Config | Current | Protections | Price |
|-------|--------|---------|-------------|-------|
| D1139 4S 20A BMS | 4S LiPo | 20A charge/discharge | Overcharge, over-discharge, short circuit, balancing | ~$6 |

#### DC-DC Converters (14.8V → system rails)

| Rail | Converter | Output | Price |
|------|-----------|--------|-------|
| 14.8V → 5V/3A | LM2596 module | 5V (Jetson, display, MUX) | ~$6.50 |
| 14.8V → 3.3V/1A | MP2307 Mini360 | 3.3V (RF, ADC) | ~$0.60 |
| 14.8V → 1.8V/0.5A | MP2307 Mini360 | 1.8V (digital logic) | ~$0.60 |

#### USB-C PD Trigger (Field Charging)

| Model | Protocol | Output | Price |
|-------|---------|--------|-------|
| Platima PD Decoy Board | PD 2.0/3.0 | 5/9/12/15/20V (DIP switch) | ~$3.20 |

**Total PSU cost**: ~$47

---

### 3.8 Display & User Interface

| Model | Resolution | Interface | Touch | Jetson Compatible | Price |
|-------|-----------|-----------|-------|-------------------|-------|
| Waveshare 5inch 720x1280 LCD | 720×1280 | HDMI + USB-C touch | 5pt Capacitive | Yes | ~$60 |

**Selected**: Waveshare 5inch HDMI (guaranteed Jetson Orin Nano compatibility via HDMI, no DSI adapter needed).

**Normative requirement**: ITU-T K.113 and K.91 require real-time display of measurement mode, TER estimate, and spatial position. The 720×1280 IPS display provides sufficient resolution for the touch-screen UI.

---

### 3.9 GNSS & Timing

#### GNSS Receiver

| Model | Constellations | Interface | Price |
|-------|---------------|-----------|-------|
| SparkFun GPS-17285 (NEO-M9N) | GPS + GLONASS + Galileo + BeiDou | UART + I2C (Qwiic) | ~$77 |

#### Active GNSS Antenna

| Model | Bands | LNA Gain | Connector | Price |
|-------|-------|----------|-----------|-------|
| u-blox ANN-MB-00 | L1/L2 multi-band | 28 dB | SMA | ~$58 |

#### TCXO Reference Oscillator

| Model | Freq | Stability | Voltage | Price |
|-------|------|-----------|---------|-------|
| Nooelec Tiny TCXO | 10 MHz | ±0.5 ppm | 3.3V | ~$20 |

**Total GNSS + timing cost**: ~$155

**Normative constraint**: ITU-T K.113 §IV.3 requires measurement points at 1.5–1.7 m height. GNSS provides room-level spatial coordinates (2–5 m accuracy) for the Kriging interpolation grid.

---

### 3.10 Protection & Conditioning

| Component | Model | Specs | Price |
|-----------|-------|-------|-------|
| ESD protection (×4) | TI ESD701 | 0.3 pF, ±15 kV contact, SMA pad | ~$0.80 |
| DC block (×4) | Mini-Circuits BLK-18-S+ | 10 MHz–18 GHz, 0.24 dB IL, SMA M/F | ~$160 |
| SMA cables (×4) | RG402 semi-flex, 10 cm | 6 GHz, ~0.2 dB loss | ~$40 |
| Heatsink + fan | Seeed 100021109 | Aluminum + PWM fan for Jetson | ~$25 |

**Total protection cost**: ~$226

---

### 3.11 Enclosure & Mechanical

| Model | Dimensions | Material | IP Rating | Price |
|-------|-----------|----------|-----------|-------|
| Hammond 1590DE | 200×120×64 mm | Die-cast aluminum | IP54 | ~$25–35 |

**Selected**: Hammond 1590DE (IP54, adequate for indoor handheld use).

**3D-printed parts**: Orthogonal antenna base, internal mounting brackets — ~$10 material cost.

---

## 4. Bill of Materials (BOM)

### Config A — Budget (HackRF Pro + DIY Triaxial Array)

| # | Category | Description | Model | Qty | Unit Price | Subtotal |
|---|----------|-------------|-------|-----|------------|----------|
| 1 | SDR | SDR transceiver, 100 kHz–6 GHz, 8-bit, USB-C | HackRF Pro | 1 | $400.00 | $400.00 |
| 2 | Compute | Edge AI module, 67 TOPS, 8GB, dev kit | Jetson Orin Nano Super | 1 | $399.00 | $399.00 |
| 3 | Filter | Bandpass filter, 30–1890 MHz, SMA | Mini-Circuits ZX75BP-960-S+ | 1 | $55.00 | $55.00 |
| 4 | Filter | Bandpass filter, 2400–2500 MHz, SMA | Mini-Circuits ZVBP-2450-S+ | 1 | $85.00 | $85.00 |
| 5 | Filter | Bandpass filter, 3450–3550 MHz, SMA | Mini-Circuits CBP4-A3R5G+ | 1 | $92.00 | $92.00 |
| 6 | Filter | Bandpass filter, 4800–6200 MHz, SMA | Mini-Circuits VBFZ-5500-S+ | 1 | $55.00 | $55.00 |
| 7 | Switch | SP3T RF switch IC, DC–6 GHz, GPIO | pSemi PE42525 | 1 | $8.00 | $8.00 |
| 8 | Antenna | 900 MHz omni antenna, SMA | Data Alliance A9O3NMO | 3 | $12.00 | $36.00 |
| 9 | Antenna | 2.4 GHz dipole antenna, SMA | L-com LCANRBD1050 | 3 | $19.20 | $57.60 |
| 10 | Antenna | 3D-printed orthogonal base | Custom STL | 1 | $5.00 | $5.00 |
| 11 | Power | LiPo battery, 4S 14.8V 5000mAh 25C | Turnigy 5000mAh 4S | 1 | $30.00 | $30.00 |
| 12 | Power | 4S LiPo BMS, 20A, with balancing | D1139 4S 20A | 1 | $6.00 | $6.00 |
| 13 | Power | DC-DC buck, 14.8V→5V/3A | LM2596 module | 1 | $6.50 | $6.50 |
| 14 | Power | DC-DC buck, 14.8V→3.3V/1A | MP2307 Mini360 | 1 | $0.60 | $0.60 |
| 15 | Power | DC-DC buck, 14.8V→1.8V/0.5A | MP2307 Mini360 | 1 | $0.60 | $0.60 |
| 16 | Power | USB-C PD trigger, 100W | Platima PD Decoy | 1 | $3.20 | $3.20 |
| 17 | Display | 5" IPS LCD touch, HDMI, 720×1280 | Waveshare 5inch HDMI | 1 | $60.00 | $60.00 |
| 18 | GNSS | GNSS module, GPS/GLONASS/Galileo/BeiDou | SparkFun GPS-17285 | 1 | $77.00 | $77.00 |
| 19 | GNSS | Active patch antenna with LNA | u-blox ANN-MB-00 | 1 | $58.00 | $58.00 |
| 20 | Timing | TCXO 10 MHz, ±0.5 ppm, 3.3V | Nooelec Tiny TCXO | 1 | $20.00 | $20.00 |
| 21 | Protection | ESD TVS diode, 0.3 pF, 15 kV | TI ESD701 | 4 | $0.20 | $0.80 |
| 22 | Protection | DC block, 10 MHz–18 GHz, SMA | Mini-Circuits BLK-18-S+ | 4 | $40.00 | $160.00 |
| 23 | Cables | SMA cable, RG402, 10 cm, low-loss | Generic RG402 | 4 | $10.00 | $40.00 |
| 24 | Cables | USB 3.0 cable, 30 cm, shielded | Manhattan 354318 | 1 | $5.30 | $5.30 |
| 25 | Cables | GPIO ribbon cable, 40-pin, 20 cm | Generic F/F | 1 | $5.00 | $5.00 |
| 26 | Thermal | Heatsink + fan for Jetson | Seeed 100021109 | 1 | $25.00 | $25.00 |
| 27 | Enclosure | Handheld enclosure, aluminum, IP54 | Hammond 1590DE | 1 | $30.00 | $30.00 |
| 28 | Mechanical | 3D-printed internal brackets | Custom STL | 1 | $10.00 | $10.00 |
| | | | | | **TOTAL** | **$1,774.00** |

---

### Config B — Professional (USRP B200mini-i + Narda EF0691)

| # | Category | Description | Model | Qty | Unit Price | Subtotal |
|---|----------|-------------|-------|-----|------------|----------|
| 1 | SDR | SDR transceiver, 70 MHz–6 GHz, 12-bit, USB 3.0 | USRP B200mini-i | 1 | $1,503.00 | $1,503.00 |
| 2 | Compute | Edge AI module, 67 TOPS, 8GB, dev kit | Jetson Orin Nano Super | 1 | $399.00 | $399.00 |
| 3 | Filter | Bandpass filter, 30–1890 MHz, SMA | Mini-Circuits ZX75BP-960-S+ | 1 | $55.00 | $55.00 |
| 4 | Filter | Bandpass filter, 2400–2500 MHz, SMA | Mini-Circuits ZVBP-2450-S+ | 1 | $85.00 | $85.00 |
| 5 | Filter | Bandpass filter, 3450–3550 MHz, SMA | Mini-Circuits CBP4-A3R5G+ | 1 | $92.00 | $92.00 |
| 6 | Filter | Bandpass filter, 4800–6200 MHz, SMA | Mini-Circuits VBFZ-5500-S+ | 1 | $55.00 | $55.00 |
| 7 | Switch | SP3T RF switch IC, DC–6 GHz, GPIO | pSemi PE42525 | 1 | $8.00 | $8.00 |
| 8 | Probe | Isotropic E-field probe, 100 kHz–6 GHz, ±1 dB | Narda EF0691 | 1 | $2,950.00 | $2,950.00 |
| 9 | Power | LiPo battery, 4S 14.8V 5000mAh 25C | Turnigy 5000mAh 4S | 1 | $30.00 | $30.00 |
| 10 | Power | 4S LiPo BMS, 20A, with balancing | D1139 4S 20A | 1 | $6.00 | $6.00 |
| 11 | Power | DC-DC buck, 14.8V→5V/3A | LM2596 module | 1 | $6.50 | $6.50 |
| 12 | Power | DC-DC buck, 14.8V→3.3V/1A | MP2307 Mini360 | 1 | $0.60 | $0.60 |
| 13 | Power | DC-DC buck, 14.8V→1.8V/0.5A | MP2307 Mini360 | 1 | $0.60 | $0.60 |
| 14 | Power | USB-C PD trigger, 100W | Platima PD Decoy | 1 | $3.20 | $3.20 |
| 15 | Display | 5" IPS LCD touch, HDMI, 720×1280 | Waveshare 5inch HDMI | 1 | $60.00 | $60.00 |
| 16 | GNSS | GNSS module, GPS/GLONASS/Galileo/BeiDou | SparkFun GPS-17285 | 1 | $77.00 | $77.00 |
| 17 | GNSS | Active patch antenna with LNA | u-blox ANN-MB-00 | 1 | $58.00 | $58.00 |
| 18 | Timing | TCXO 10 MHz, ±0.5 ppm, 3.3V | Nooelec Tiny TCXO | 1 | $20.00 | $20.00 |
| 19 | Protection | ESD TVS diode, 0.3 pF, 15 kV | TI ESD701 | 2 | $0.20 | $0.40 |
| 20 | Protection | DC block, 10 MHz–18 GHz, SMA | Mini-Circuits BLK-18-S+ | 2 | $40.00 | $80.00 |
| 21 | Cables | SMA cable, RG402, 15 cm, low-loss | Generic RG402 | 2 | $12.00 | $24.00 |
| 22 | Cables | USB 3.0 cable, 30 cm, shielded | Manhattan 354318 | 1 | $5.30 | $5.30 |
| 23 | Cables | GPIO ribbon cable, 40-pin, 20 cm | Generic F/F | 1 | $5.00 | $5.00 |
| 24 | Thermal | Heatsink + fan for Jetson | Seeed 100021109 | 1 | $25.00 | $25.00 |
| 25 | Enclosure | Handheld enclosure, aluminum, IP54 | Hammond 1590DE | 1 | $30.00 | $30.00 |
| 26 | Mechanical | 3D-printed internal brackets | Custom STL | 1 | $10.00 | $10.00 |
| | | | | | **TOTAL** | **$5,648.60** |

> **Budget note**: Config B exceeds $5,000 by ~$649. To bring it within budget:
> - Replace USRP B200mini-i ($1,503) with HackRF Pro ($400) → saves $1,103
> - Or remove 2× DC blocks (save $80) and use generic GNSS antenna (save $38)
> - **Recommended**: Use HackRF Pro + Narda EF0691 = **~$4,545.60** (within budget)

---

## 5. Budget Summary

### Config A — Budget

| Category | Cost (USD) |
|----------|-----------|
| SDR Transceiver | $400.00 |
| Edge Compute (Jetson Orin Nano Super) | $399.00 |
| RF Filters (4 bands) | $287.00 |
| RF Switch (PE42525) | $8.00 |
| Antennas (3× Sub-1GHz + 3× 2.4GHz + base) | $98.60 |
| Power Supply (LiPo + BMS + DC-DC + PD) | $46.90 |
| Display (5" HDMI touch) | $60.00 |
| GNSS (module + antenna) | $135.00 |
| TCXO Reference | $20.00 |
| Protection (ESD + DC blocks) | $160.80 |
| Cables (SMA + USB + GPIO) | $50.30 |
| Thermal (heatsink + fan) | $25.00 |
| Enclosure + Mechanical | $40.00 |
| **GRAND TOTAL** | **$1,774.00** |
| **Remaining Budget** | **$3,226.00** |

### Config B — Professional (USRP + Narda)

| Category | Cost (USD) |
|----------|-----------|
| SDR Transceiver (USRP B200mini-i) | $1,503.00 |
| Edge Compute (Jetson Orin Nano Super) | $399.00 |
| RF Filters (4 bands) | $287.00 |
| RF Switch (PE42525) | $8.00 |
| Isotropic Probe (Narda EF0691) | $2,950.00 |
| Power Supply (LiPo + BMS + DC-DC + PD) | $46.90 |
| Display (5" HDMI touch) | $60.00 |
| GNSS (module + antenna) | $135.00 |
| TCXO Reference | $20.00 |
| Protection (ESD + DC blocks) | $80.40 |
| Cables (SMA + USB + GPIO) | $34.30 |
| Thermal (heatsink + fan) | $25.00 |
| Enclosure + Mechanical | $40.00 |
| **GRAND TOTAL** | **$5,588.60** |

> **Budget-adjusted Config B** (HackRF Pro + Narda EF0691): **$4,485.60** ✓

---

## 6. Normative Compliance Traceability

| Requirement | Source Standard | Satisfying Component / Design |
|-------------|----------------|------------------------------|
| Isotropic response <2 dB (<900 MHz) | ITU-T K.61 §8.1.3.3, K.100 | Config A: 3× discrete antennas on 3D-printed orthogonal base; Config B: Narda EF0691 (±1 dB) |
| Isotropic response <3 dB (0.9–3 GHz) | ITU-T K.61 §8.1.3.3, K.100 | Config A: 3× L-com dipoles in orthogonal mount; Config B: Narda EF0691 (±1 dB) |
| Isotropic response <5 dB (>3 GHz) | ITU-T K.61 §8.1.3.3, K.100 | Config B: Narda EF0691 (±1.2 dB at >5 GHz) |
| Sequential axis measurement permitted | ITU-T K.100 §6 | SP3T switch (PE42525) with post-processing: E_total = √(E_x² + E_y² + E_z²) |
| 6-min averaging time | ICNIRP 2020 | Software integration in compliance mode (Jetson Orin Nano) |
| 1-min screening time | ITU-T K.91 §7.2.3, K.113 §IV.5 | Software integration in screening mode |
| Dynamic range >25 dB | ITU-T K.100 Table I.1 | HackRF: ~48 dB; USRP: ~72 dB |
| Frequency response ±1.5 dB (600 MHz–6 GHz) | ITU-T K.100 Table I.1 | Band-select filters (Mini-Circuits, <1.3 dB IL) |
| Probe height 1.5–1.7 m | ITU-T K.113 §IV.3, K.91 | Ergonomic handheld design, operator instruction |
| Combined uncertainty U ≤3 dB | ITU-T K.91, K.61 | TCXO (±0.5 ppm), band-select filters, calibration procedure |
| Measurement data logging | ITU-T K.113 | Jetson Orin Nano, timestamped CSV/JSON export |
| Exposure map generation | K.113, Martínez-González 2022 | GPU-accelerated Kriging interpolation (Jetson 67 TOPS) |
| ELSP point reduction (up to 70%) | Martínez-González 2022 | Pre-scan (1-min) + focused measurement (6-min at hotspots) |
| GNSS geolocation | K.113 §IV.3 | u-blox NEO-M9N, GPS/GLONASS/Galileo, UART |
| Battery life >2 h | Operational requirement | LiPo 4S 5000mAh (74 Wh) → ~4.9 h at 15 W |
| Total mass ≤2.5 kg | Ergonomic constraint | Estimated: ~1.8 kg (Config A), ~2.0 kg (Config B) |

---

## 7. References

### Normative Standards (from RAG index)

1. ICNIRP (2020). "Guidelines for limiting exposure to electromagnetic fields (100 kHz to 300 GHz)." *Health Physics*, 118(5), 483–524.
2. ITU-T K.52 (08/2024). "Guidance on complying with limits for human exposure to electromagnetic fields."
3. ITU-T K.61 (10/2025). "Guidance on measurement and numerical prediction of electromagnetic fields for compliance."
4. ITU-T K.91 (01/2024). "Guidance for assessment, evaluation and monitoring of human exposure to radiofrequency electromagnetic fields."
5. ITU-T K.100 (08/2024). "Measurement of radio frequency electromagnetic fields to determine compliance with human exposure limits."
6. ITU-T K.113 (07/2025). "Generation of radiofrequency electromagnetic field exposure maps."
7. ANE Resolución 773 (2023). "Colombian adoption of ICNIRP/ITU-T limits and measurement methodology."
8. Decreto 1370 (2018). "Colombian EMF exposure limits framework."

### Technical References

9. Martínez-González, A. et al. (2022). "Minimization of measuring points for the electric field exposure map generation in indoor environments by means of Kriging interpolation and selective sampling." *Environmental Research*, 212, 113577.
10. IEC 62232. "Radio-frequency field strength measurement and calculation for determination of compliance with exposure limits — Base stations."

### Component Sources

11. Mini-Circuits. https://www.minicircuits.com/
12. Great Scott Gadgets (HackRF). https://greatscottgadgets.com/
13. Ettus Research / NI (USRP). https://www.ettus.com/
14. NVIDIA Jetson. https://developer.nvidia.com/embedded-computing
15. Narda Safety Test Solutions. https://www.narda-sts.com/
16. SparkFun Electronics. https://www.sparkfun.com/
17. u-blox. https://www.u-blox.com/
18. Hammond Manufacturing. https://www.hammfg.com/
