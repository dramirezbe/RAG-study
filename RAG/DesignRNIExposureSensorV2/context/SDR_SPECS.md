# PlutoSky R2 SDR Specifications

## 📌 Product Overview
The PlutoSky R2 is a high-performance Software Defined Radio (SDR) based on the ADALM-Pluto architecture. It features a Xilinx Zynq SoC combining a dual-core ARM Cortex-A9 processor with FPGA programmable logic, making it highly versatile for SDR applications.

---

## 🚀 Main Hardware Updates (vs. Previous Versions)
1. **Upgraded Main Chip**: Replaced with **XC7Z020-2CLG484I** (formerly XC7Z020-2CLG400I). While retaining the same Z-7020 core resources, it offers significantly more usable external I/O.
2. **10MHz Reference Clock**: Added a 10MHz reference clock input to lock and calibrate the onboard crystal oscillator.
3. **External 40MHz Clock Support**: 
   - *Flash Firmware Mode*: Can use an external 40MHz clock by short-circuiting a specific area with a 2.54mm jumper cap (must be done while powered off).
   - *SD Card Firmware Mode (e.g., Tezuka)*: Users can use GPIO to control whether to use the external 40MHz clock or the internal onboard 40MHz clock.
4. **Optimized Network Port**: The Ethernet port has been moved to the **PL end** (FPGA programmable logic) instead of the traditional PS end (Processor System). This allows customers to directly use logical resources for data transmission without ARM core involvement, achieving true data transparency.

---

## 📊 Hardware Specifications

### Variant Options
| SKU Code | RFIC | Power Amplifier (PA) | LO Frequency | Bandwidth |
| :--- | :--- | :--- | :--- | :--- |
| **PLUTOSKY-R2-9361-NOPA** | AD9361BBCZ | No | 70 MHz - 6 GHz | 200 kHz - 56 MHz |
| **PLUTOSKY-R2-9363-NOPA** | AD9363BBCZ | No | 70 MHz - 6 GHz *(with hack)* | 200 kHz - 20 MHz |
| **PLUTOSKY-R2-9361-PA** | AD9361BBCZ | Yes (10dB gain, Bypassable) | 70 MHz - 6 GHz | 200 kHz - 56 MHz |
| **PLUTOSKY-R2-9363-PA** | AD9363BBCZ | Yes (10dB gain, Bypassable) | 70 MHz - 6 GHz *(with hack)* | 200 kHz - 20 MHz |

### Core System Specifications
| Component | Specification |
| :--- | :--- |
| **Processor (SoC)** | Xilinx Zynq XC7Z020-2CLG484I (Dual-core ARM Cortex-A9 + FPGA) |
| **RF Interface** | 2T2R (Full-duplex) |
| **Memory (DDR3)** | 1 GB |
| **Storage (FLASH)** | 32 MB |
| **Extended I/O** | 19 × 1.8V I/O pins |

### Connectivity & Interfaces
| Interface | Quantity / Type |
| :--- | :--- |
| **Ethernet** | 1 × 1000/100/10M ETH (Connected to PL end) |
| **USB** | 1 × USB 2.0 (Type-C) |
| **UART + JTAG** | 1 × (Type-C) |
| **SD Card Slot** | 1 × (For PS Boot) |

### Physical & Power
| Feature | Specification |
| :--- | :--- |
| **Dimensions** | 96 × 64 mm (CNC machined) |
| **Power Supply** | USB, JTAG, or Pin header |

---

## ⏱️ Clock & Timing Specifications
- **Default Clock**: Onboard VCTCXO.
- **Alternative Clock**: IPEX connector option available.
- **Clock Calibration**: Supports calibrating the onboard clock with a **10MHz external input**. *(Note: PPS / Pulse Per Second input calibration is **not** supported).*
- **Clock Switching**: Supports switching between internal onboard 40MHz clock and external 40MHz clock via GPIO settings or jumper caps (depending on firmware boot mode).
- **Internal IPEX Connectors**: The board features three internal IPEX connectors for:
  1. 40MHz clock input
  2. TX local oscillator
  3. RX local oscillator

---

## ℹ️ Frequently Asked Questions (FAQs) Summary
- **Does it support PPS input?** No, PPS calibration is not supported, but 10MHz reference input calibration is supported.
- **Can I switch between internal and external clocks?** Yes, the PlutoSky R2 supports GPIO settings (or jumper caps, depending on firmware) to determine whether to use the onboard 40MHz clock or an external 40MHz clock.
