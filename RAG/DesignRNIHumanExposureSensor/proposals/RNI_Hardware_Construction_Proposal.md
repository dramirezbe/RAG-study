# RNI Hardware Construction Proposal

**System:** SDR-Based RNI Human Exposure Monitoring System  
**Institutions:** Universidad Nacional de Colombia (Manizales) and Universidad de Caldas  
**Proposal and web-access date:** 2026-08-25  
**Budget ceiling:** USD 5,000

## Executive recommendation

Build a portable **frequency-selective exposure-monitoring research prototype** around one Ettus **USRP B206mini-i**, one **Jetson Orin Nano Super 8 GB Developer Kit**, a two-stage pSemi **PE42442 SP4T** RF switching board, four band filters, and three orthogonally mounted Taoglas **TG.66.A113** wideband monopoles. Use a Waveshare 7-inch HDMI/USB touchscreen and a protected Bioenno **BLF-1215A 180 Wh LiFePO4** pack.

The RF order is antenna axis switch → overload pad/protection → band-filter switch → SDR. The digital and power paths are physically separate. No LNA is fitted in the baseline. The 10 dB pad is removable only under a controlled, separately calibrated weak-signal configuration.

This choice covers the four mandatory bands, retains raw I/Q and frequency selectivity, supports sequential XYZ acquisition, and totals **$3,660.94 hardware plus $500 contingency = $4,160.94**. It is **not a compliance instrument** at construction. Its E-field output is valid only after end-to-end radiated calibration, uncertainty evaluation, and comparison with calibrated reference instrumentation.

## 1. Requirements and design boundary

The repository scope requires spectral monitoring, frequency-selective analysis, IoT-band characterization, local DSP/ML, logging, mapping and portable field campaigns. The principal measurand is RMS electric-field strength **E [V/m]**. The design supports:

- B1 902–928 MHz: LoRaWAN, Z-Wave and sub-GHz Zigbee.
- B2 2.400–2.4835 GHz: Wi-Fi, Bluetooth LE/Mesh, Zigbee/Thread, WirelessHART and ISA100.11a.
- B3 3.300–3.700 GHz: indoor/private 5G.
- B4 5.150–5.850 GHz: Wi-Fi 5/6 and industrial WLAN.
- Screening, hotspot search, 1-minute exploratory and 6-minute stored averages, campaign metadata, exposure-ratio calculation, Kriging preprocessing and local display.

The optional 5.925–7.125 GHz Wi-Fi 6E/7 band is **not baseline**: the chosen SDR and PE42442 stop at 6 GHz, and the B4 filter stops at 6.2 GHz. Full extension requires a ≥7.125 GHz receiver, switch, antenna-factor calibration and filter path. It is an upgrade, not a software unlock.

The project scope originally mentions approximately 700 MHz–3.5 GHz and a USRP B200mini/TG.62-style concept; this task expands mandatory coverage to 5.85 GHz. The selected B206mini-i is the current B200mini-family successor. The repository’s `LIST_IOT_SERVICES.md` establishes the technology mapping above.

## 2. Normative traceability

PDF page numbers below are physical PDF pages verified from the local normative copies. Requirements are conservative paraphrases; no unverified clause/page is asserted.

| Document | Verified location | Requirement/paraphrase | Engineering consequence |
|---|---|---|---|
| ICNIRP 2020 | PDF pp. 9–18; tables 2 and 6 are on PDF pp. 10 and 15 | RF restrictions/reference levels use frequency-dependent quantities and prescribed averaging; 6 minutes is used for local exposure/reference-level treatment in the relevant range, while whole-body treatment can differ | Store raw time series and calculate the correct frequency-dependent RMS/exposure metric; do not treat every campaign statistic as a generic 6-minute arithmetic average |
| ANE Resolution 773/2023 | PDF pp. 18, 21, 35, 37–38 | Defines exposure ratio; states 6-minute averaging in the cited note; its procedure discusses when a 1-minute reduction can be acceptable and detailed 6-minute capture | Provide 1-minute screening mode only as labeled screening, retain 6-minute assessment mode and bandwise ER/TER calculation |
| Decreto 1370/2018 | Clause/page for an instrument-specific requirement **NOT VERIFIED** | Colombian legal framework assigns EMF-limit/control responsibilities; no direct SDR, probe, filter or ADC prescription was verified | Treat it as jurisdictional authority, not a component specification; apply current ANE procedure and documented limits |
| ITU-T K.52 (08/2024) | §§6.2, 9.4, 10.2–10.3; PDF pp. 13 and 21 verified | Exposure limits depend on averaging time; assessment accounts for simultaneous sources; measurement/calculation uncertainty must be considered; plane-wave S–E relation is conditional | Time-tag all sources/bands, combine ratios properly, and derive S=E²/377 only in justified far-field plane-wave conditions |
| ITU-T K.61 (10/2025) | §8.1.3, PDF pp. 15–17; isotropic-probe discussion PDF p. 19 | Calibrated instruments and calibration factors are required; antenna/probe response, linearity and uncertainty contributions matter | Calibrate every axis/path/frequency; archive certificates and correction tables; nominal antenna gain is not antenna factor |
| ITU-T K.91 (01/2024) | §§5.8–5.10 and uncertainty treatment; PDF pp. 17, 19–20, 32–36 and 71 | Required receiver range follows source levels; total exposure ratio from simultaneous sources should be below 1; broadband and selective measurements have different roles; calibration drift enters uncertainty | Use attenuator/filtering for headroom; compute band-resolved ER then TER; compare broadband reference and selective SDR results without conflating them |
| ITU-T K.100 (08/2024) | §§6, 9.7 and Annex I; PDF pp. 12, 22–29 | Broadband screening may require frequency-selective follow-up; either broadband or selective equipment can be used in context; calibration at sufficient frequencies and TER determination are required | The SDR is the selective follow-up chain; sweep calibration frequencies across each band and retain bandwidth/RBW settings |
| ITU-T K.113 (07/2025) | §§6–7 and Annex IV; PDF pp. 15, 17–18, 21, 26–29 | Mapping uses an isotropic probe; calibration/QC are required; examples specify 1.5–2.0 m probe height, ≥6-minute readings, and report instrument/probe/certificate information; a 1-minute alternative may be used when appropriate | Set a nonconductive mast at documented height, store position/time/environment/instrument IDs, use six-minute campaign records, and label the discrete XYZ result as calibrated synthesized isotropy rather than inherent probe isotropy |

No local text supports a universal claim that sequential axes are normatively identical to simultaneous acquisition under every field variation. The baseline therefore timestamps each axis, cycles quickly relative to the averaging interval, records the switching schedule, and includes axis-sequence sensitivity in uncertainty. If emitters vary faster than the cycle, simultaneous three-channel reception is the upgrade path.

## 3. Conceptual-image audit

The attached image is useful as a packaging concept, not signal flow. Corrections are:

1. Incident RF enters the antenna first; the filter bank cannot sit between the SDR and antenna in an ambiguous direction.
2. XYZ selection precedes a common receiver in the sequential architecture; a second SP4T selects one of four filters.
3. Protection/attenuation belongs ahead of the sensitive SDR. A filter protects ADC headroom against out-of-band energy but not necessarily ESD or all high in-band fields.
4. The SDR sends digital I/Q over USB to Jetson; the Jetson does not pass analog RF.
5. Display is a digital sink/control surface, not in the acquisition link.
6. Battery/PSU is a separate star-distributed power tree. It never appears in the RF path.
7. “Jetson Orin,” “HackRF One,” “Hosyond,” `TG664133` and “triaxial antenna” are candidates, not complete part definitions.
8. Narda/Wavecontrol broadband probes output detected/proprietary signals and are not interchangeable with a 50-ohm SDR antenna port.

## 4. Corrected architecture

```text
RF ANALOG PATH

Incident E field
  -> TG.66 X / Y / Z elements in calibrated orthogonal fixture
  -> PE42442 SP4T axis selector (fourth port terminated/test input)
  -> removable VAT-10A+ 10 dB pad + PCB ESD/limiter footprint
  -> PE42442 SP4T band selector
       -> ZX75BP-915-S+  [B1]
       -> VBF-2450+      [B2]
       -> BFCN-3600+     [B3]
       -> VBFZ-5500-S+   [B4]
  -> USRP B206mini-i RX

DIGITAL DATA / CONTROL PATH

USRP --USB 3 I/Q--> Jetson --DP/HDMI--> display
                         |--USB HID touch
                         |--GPIO/level interface--> axis/filter switches
                         |--NVMe--> I/Q, PSD, calibration, campaign database
                         `--Ethernet/Wi-Fi--> export/network services

POWER PATH

BLF-1215A 12 V battery -> main fuse + master switch + distribution
  |-> Jetson DC input
  |-> 5 V / 9 A buck -> display, RF-control PCB, auxiliaries
  `-> fused service/monitor ports

14.6 V charger -> battery charge port (instrument off unless power-path tested)
```

The filter switch is arranged so the selected filter is in series, not as four unterminated stubs. Both SP4Ts are absorptive. Shield cans and compartment partitions reduce Jetson/display emissions entering the antenna/SDR chain.

## 5. Construction architectures considered

| Criterion | A: one SDR + switched XYZ/filter bank | B: band-specific XYZ front ends | C: modular receiver/RF cartridges |
|---|---|---|---|
| Measurement capability | Four band-resolved sequential axes | Best per-band sensitivity/match; possible parallel paths | Depends on installed cartridge |
| Dynamic range | Good with filters/pad; one ADC | Best optimization, highest channel count | Good but configuration-dependent |
| Calibration/traceability | 12 axis-band states; manageable | At least 12 antenna/front-end chains; expensive | Each cartridge separately calibrated |
| Normative suitability | Suitable for research/selective screening after calibration | Best route to simultaneous axes | Good records needed to avoid mixed configurations |
| Portability/power | Best | Worst | Intermediate |
| Mechanical/software complexity | Moderate | High/high | High initially, lower maintenance later |
| Maintainability/upgrade | Replaceable filters/antennas; one receiver | Many spares and drift paths | Best upgradeability |
| Estimated hardware | ~$3.66k | Likely >$5k with multiple receivers/switching | ~$4–6k depending modules |

**Architecture A is selected.** Architecture B is preferred only if fast axis simultaneity becomes a validated requirement. Architecture C is the long-term 6E/7 and mmWave path.

## 6. Component decisions

### SDR

The B206mini-i covers 70 MHz–6 GHz with a 12-bit AD9364, 56 MHz instantaneous bandwidth, USB 3 Type-C, UHD and external 10 MHz/PPS. It has ample bandwidth to capture each narrow B1/B2 band at once and to segment B3/B4 into overlapping, calibrated subspans. It supersedes the cheaper B200mini-i in the manufacturer catalog.

HackRF One covers the bands but its 8-bit I/Q and USB 2 interface reduce overload margin and throughput. Pluto and LimeSDR Mini 2.0 do not cover B4. ADC bit depth only bounds quantization; effective number of bits, front-end noise figure, gain state, intermodulation and compression govern usable dynamic range. The B206 is suitable for spectrum sensing and for calibrated research measurements; it is not independently a calibrated exposure meter.

### Edge processor

The Orin Nano Super is the lowest-cost adequate Jetson. At 15 W it supports sustained USB acquisition, FFT/PSD, calibration lookup, sequential-axis combination, time averaging, logging, GUI and modest PyTorch/TensorRT inference. Kriging map generation is not a hard real-time RF requirement and can be decoupled from acquisition. Acceptance testing must prove zero dropped samples at the chosen rate while the GUI and storage operate. Orin NX adds cost/power without a measurement benefit for this baseline.

### Display

Waveshare SKU 11199 replaces Hosyond. Both nominally offer 7-inch 1024×600 HDMI/USB touch, but Waveshare supplies a stable SKU, product resources and a $43.99 manufacturer price. Jetson Orin Nano provides DisplayPort, so the BOM includes a DP-to-HDMI adapter. Linux HID touch, brightness, current and EMC are bench-qualified. Hosyond’s exact image MPN and power/mounting data remain not verified.

### Filter bank

- B1 ZX75BP-915-S+ is 902.5–927.5 MHz. It leaves two 0.5 MHz regulatory-band edge slivers. The software must either make a bypass/alternate-filter edge scan or label baseline guaranteed filtered coverage as 902.5–927.5 MHz. An exact 902–928 MHz quoted filter is the corrective procurement option.
- B2 VBF-2450+ covers 2400–2550 MHz, encompassing the full target.
- B3 BFCN-3600+ covers 3300–3900 MHz. It is SMT, so it is mounted on the controlled-impedance switch PCB; PCB/connector loss is part of calibration.
- B4 VBFZ-5500-S+ covers 4900–6200 MHz, encompassing the full target.

Each path receives VNA S-parameter characterization at operating temperature. Manufacturer rejection figures are design evidence, not a substitute for incoming measurements. The narrower $455 ZVBP-2450A-S+ is retained when stronger adjacent-service rejection is demonstrated necessary.

### Switching

Two PE42442A-Z absorptive SP4Ts implement `XYZ/test` and `B1/B2/B3/B4`. They cover 30 MHz–6 GHz, have stated 0.85–2.35 dB insertion-loss range, 32–67 dB isolation range, 58 dBm IIP3, 2.3–5.5 V supply, and 0.255 μs switching. GPIO control is latched, state-verified, and logged. Settling wait is conservatively ≥1 ms until measured. The fourth axis port accepts a 50-ohm termination or calibration injection.

### Antenna option 1: discrete XYZ

The image’s “TG664133” is interpreted as a likely reference to **Taoglas TG.66.A113**; no exact product named TG664133 was verified. TG.66.A113 is a 600–6000 MHz hinged 50-ohm SMA monopole. Three elements cover every mandatory band electrically.

They are mounted with element axes orthogonal, identical cable routing, a low-dielectric central fixture, and a reproducible local ground/reference structure. At 902 MHz the free-space wavelength is ~332 mm; the 70 mm elements and nearby grounds/cables strongly affect patterns. At 5.85 GHz the wavelength is ~51 mm, so millimetric fixture/cable differences matter. Close orthogonal placement causes mutual coupling and pattern distortion; wide spacing improves coupling but enlarges the sensing volume and spatial non-coincidence. Geometry is frozen before calibration.

Three TG.66 units **can produce three polarization-sensitive channels**, but cannot claim isotropy or calibrated E-field from gain plots alone. Required radiated calibration yields complex/effective height or antenna factor for every axis and orientation. The synthesized result `sqrt(Ex²+Ey²+Ez²)` is accepted only after rotation tests quantify residual isotropy error.

### Antenna option 2: commercial triaxial/isotropic

- Aaronia IsoLOG 3D Mobile 9060 PRO has a 50-ohm N RF output, internal axis selection, USB control and bypassable preamps. It is directly SDR-compatible and the best commercial integration alternative. At €1,998/$2,697.30 it would consume most remaining budget, and public evidence did not establish an accredited E-field antenna factor/uncertainty. It is not selected.
- Narda EF0691 uses three detector-diode dipoles with individual detected component voltages and EPROM correction data consumed by an NBM base unit. It is **NOT DIRECTLY SDR-COMPATIBLE** and cannot provide frequency-selective I/Q.
- Wavecontrol WPF8 is an ISO-17025-calibratable isotropic RMS diode probe for its proprietary meter ecosystem. It is **NOT DIRECTLY SDR-COMPATIBLE**.

### Weighted antenna decision

Scores are 0–10; weighted totals are out of 10. “Commercial probe” uses the best SDR-compatible candidate, Aaronia 9060 PRO, while noting its traceability gap.

| Criterion | Weight | Discrete TG.66 XYZ | Rationale | IsoLOG 9060 PRO | Rationale |
|---|---:|---:|---|---:|---|
| Normative suitability | 20% | 5 | Requires proof of synthesized isotropy | 7 | Integrated 3D selection, but compliance uncertainty not published |
| E-field capability | 20% | 5 | Antenna factor TBD | 6 | Raw RF supports conversion, factor still TBD |
| Calibration/traceability | 15% | 4 | Custom radiated calibration | 5 | Stable product, no verified accredited AF supplied |
| Frequency coverage | 15% | 10 | 600 MHz–6 GHz | 10 | 9 kHz–6 GHz |
| SDR compatibility | 10% | 10 | Direct 50-ohm SMA RF | 10 | Direct 50-ohm N RF |
| Isotropy | 5% | 4 | Must be synthesized/validated | 8 | Integrated 3D antenna claim |
| Portability | 5% | 8 | Small fixture | 8 | 350 g handheld |
| Cost | 5% | 10 | $58.23 elements | 3 | ~$2.7k US |
| Maintainability | 5% | 9 | Replaceable elements/switch | 6 | Proprietary assembly |
| **Weighted total** | **100%** | **6.55** |  | **7.10** |  |

Despite the higher technical score, the commercial option plus selected core leaves inadequate margin for complete power, filter, enclosure and calibration integration. **Discrete XYZ is the recommended baseline under the $5,000 project constraint**; IsoLOG is retained as the premium upgrade. The lower baseline score is explicitly a risk to be retired by calibration, not hidden by a compliance claim.

## 7. Metrological chain and calculations

For axis `a`, frequency bin `f`, switch/filter state `s`, the receiver first converts calibrated digital magnitude to available RF power:

```text
Pport,a(f) [dBm] = PADC,a(f)
                  - GSDR(f,gain,temperature)
                  + Lpost(f,s)
```

The E-field conversion uses a radiated calibration. Depending on the calibration laboratory’s convention:

```text
E_a(f) [dBµV/m] = Vantenna,a(f) [dBµV] + AF_a(f) [dB/m]

or

V_oc,a(f) = h_eff,a(f,orientation) * E_a(f)
```

where cable, switch, filter and pad loss are applied exactly once. Then:

```text
E_xyz(f) = sqrt(Ex(f)^2 + Ey(f)^2 + Ez(f)^2)
ER_i = (E_i / E_limit,i)^2       [when E reference levels apply]
TER = sum_i ER_i                  [using the applicable normative frequency grouping]
```

Equivalent plane-wave power density may be reported as `S = E²/377` only when far-field/local plane-wave conditions are justified. Indoor reactive/near-field or strong multipath conditions do not automatically satisfy that relation; E remains the primary measurand.

The following are **TBD — REQUIRES CALIBRATION**: TG.66 antenna factor/effective height per axis; fixture/ground-plane response; axis coupling; cable loss and phase; switch-state loss/isolation; filter loss/ripple; attenuator actual value; SDR amplitude/gain/ADC response and compression; X/Y/Z mismatch; synthesized isotropy; temperature coefficients; repeatability; frequency reference error. Calibration tables are versioned by hardware serial number and path state.

### Uncertainty sources

The budget includes antenna calibration, reference-field uniformity, alignment/polarization, probe positioning, source drift, mismatch, cable flexure, switching repeatability, filter ripple, SDR linearity/noise/quantization, gain-state repeatability, RBW/window/FFT integration, axis time skew, temperature, spatial perturbation by operator/enclosure, interpolation and reference-limit uncertainty. Numerical expanded uncertainty is **NOT VERIFIED** until measurements and calibration certificates exist. Reported results carry coverage factor and confidence level only after an ISO/IEC 17025/GUM-style budget is completed.

## 8. RF path budget and overload policy

Values below are preliminary design estimates/ranges. Exact values are replaced with VNA/calibration data. Cable estimate assumes short low-loss assemblies; PE42442 range is manufacturer-wide and conservative.

| Band | Cable | Axis switch | 10 dB pad | Filter path | Filter switch | Preliminary net loss | Consequence |
|---|---:|---:|---:|---:|---:|---:|---|
| B1 | 0.3 dB | 1.0 dB | 10.0 dB | 2.0 dB TBD | 1.0 dB | ~14.3 dB | Strong overload margin; sensitivity reduced |
| B2 | 0.5 dB | 1.2 dB | 10.0 dB | 2.1 dB typ max estimate | 1.2 dB | ~15.0 dB | Adjacent-band suppression improves ADC use |
| B3 | 0.7 dB | 1.5 dB | 10.0 dB | 2.5 dB PCB+filter TBD | 1.5 dB | ~16.2 dB | PCB/ripple dominant calibration risk |
| B4 | 1.0 dB | 2.35 dB worst-range | 10.0 dB | 1.5 dB TBD | 2.35 dB | ~17.2 dB | Highest loss; confirm weak-field floor |

The pad is not “protection” against all transients. The RF PCB includes a low-capacitance ESD/limiter footprint, but the exact limiter MPN is **NOT VERIFIED** and is not populated until its 6 GHz insertion loss/leakage and clamp behavior are qualified. Start every campaign at minimum receiver gain with pad fitted, run a compression/overload check, then increase gain. An LNA is prohibited in baseline mode; an optional LNA requires its own calibration and automatic bypass/interlock.

## 9. Power-system design

The BLF-1215A provides 180 Wh nominal. Applying a conservative combined 80% usable fraction for reserve, wiring and conversion gives **144 Wh usable**.

| Load | Rail | Typical current | Peak current | Typical W | Peak W | Source/basis |
|---|---:|---:|---:|---:|---:|---|
| Jetson Orin Nano | 12 V input equivalent | 1.25 A | 2.1 A | 15.0 | 25.0 | Selected 15 W mode; 25 W mode as peak bound |
| USRP B206mini-i | 5 V USB | 0.8 A | 1.2 A | 4.0 | 6.0 | **Engineering estimate; measure in Stage 2** |
| 7-inch display | 5 V | 0.8 A | 1.2 A | 4.0 | 6.0 | **Engineering estimate; manufacturer current NOT VERIFIED** |
| NVMe storage | 3.3 V internal | 0.6 A | 1.5 A | 2.0 | 5.0 | Engineering envelope |
| Two RF switches/control | 5 V | 0.10 A | 0.20 A | 0.5 | 1.0 | Engineering envelope |
| Fan/aux/rail monitor | 5 V | 0.30 A | 0.60 A | 1.5 | 3.0 | Engineering envelope |
| DC conversion/wiring loss | battery | — | — | 2.0 | 3.0 | Modeled loss |
| **Total** | — | — | — | **29.0 W** | **49.0 W** | Design totals |

At 29 W typical, modeled runtime is `144/29 = 4.97 h`. Design/advertised campaign duration is **4 h**, leaving approximately 19% runtime reserve. At peak load, the battery must supply about `49/12 = 4.1 A`; a 5 A main fuse is borderline with inrush, so use a time-delay 7.5 A main fuse after measuring inrush, with branch fuses sized to wiring/load. Final fuse ratings are confirmed experimentally.

| Desired operating time | Required usable energy | Nominal pack energy at 80% usable | Assessment |
|---:|---:|---:|---|
| 2 h | 58 Wh | 72.5 Wh | Easily met |
| 4 h | 116 Wh | 145 Wh | Recommended with reserve |
| 6 h | 174 Wh | 217.5 Wh | BLF-1215A insufficient at modeled load; select ≥20 Ah/256 Wh pack |

Power topology uses locking Powerpole/DC connectors, master disconnect, main and branch fuses, strain relief, 16–20 AWG distribution, voltage/current telemetry, and the protected battery’s internal PCM/BMS. Jetson is fed from the battery within its qualified input range; 5 V auxiliaries use Pololu D24V90F5. Charger is a matched 14.6 V/4 A unit. Charger operation during measurement is disabled unless conducted/radiated noise and safe power-path behavior pass validation.

## 10. Mechanical and EMC integration

Use a divided enclosure: battery at the base, Jetson/display thermal bay, and shielded RF bay with bulkhead SMA connectors. Maintain airflow without routing fan/display currents along RF grounds. Bond enclosure panels at RF, but isolate the antenna fixture on a nonconductive mast. Keep the display and Jetson behind the antenna and at maximum practical distance. Use labeled equal-length axis cables, torque-controlled SMA connections, strain relief and replaceable filter mounts.

The antenna fixture must reproduce element angle, hinge position, ground/reference plate and cable exits. A 1.5–2.0 m nonconductive tripod supports normative-style mapping; the exact campaign height is recorded. Operator separation, nearby objects and room coordinates are documented. Indoor GNSS is not baseline because it is unreliable for room-scale mapping; use surveyed floor-grid coordinates/AprilTag/UWB externally. A 10 MHz reference is optional because frequency accuracy is useful but does not replace amplitude calibration.

## 11. Complete BOM and budget verification

The authoritative line-item BOM is [`RNI_BOM.csv`](RNI_BOM.csv). It includes SDR, compute, display, storage, four filters, two fitted switch ICs plus spares, antennas, pad, battery, charger, DC/DC, enclosure, RF PCB, RF/digital cables, adapters, power distribution, fixture and calibration hardware.

| Budget line | USD |
|---|---:|
| Purchasable hardware/integration subtotal | **3,660.94** |
| Shipping/imports/fabrication/spares contingency | **500.00** |
| **Project total** | **4,160.94** |
| Budget ceiling | 5,000.00 |
| **Headroom** | **839.06** |

Engineering-allowance rows are explicitly marked `PRICE NOT VERIFIED`; they are cost ceilings, not invented commercial quotations. The design remains below the preferred $4,500 hardware target and the $5,000 absolute total.

## 12. Construction sequence

1. **Jetson/display bench validation:** install current JetPack, NVMe, DP-HDMI and USB touch; run thermal/power/load tests and EMC sniffing.
2. **SDR/Jetson integration:** build UHD/GNU Radio on aarch64; verify device enumeration, 56 MHz burst and selected sustained rates, drop counters, timestamps and file integrity.
3. **Single-band RF front end:** use B2 filter/pad, calibrated signal generator and power meter; characterize gain, noise floor, compression and amplitude repeatability.
4. **Four-band filter bank:** VNA-measure each filter/cable/PCB path; populate BFCN-3600; confirm full usable passbands and B1 edge limitation.
5. **RF switching:** populate both PE42442 stages; test truth table, terminated states, isolation, transient/settling, GPIO failsafe and calibration-injection port.
6. **Single-axis antenna validation:** fixture one TG.66; measure return loss/pattern response and derive preliminary antenna factor in a known field.
7. **XYZ structure:** freeze geometry, equalize cables, measure mutual coupling and rotation response, then implement time-stamped sequential acquisition.
8. **Portable power:** wire fuses/switch/buck/monitor; verify inrush, peak current, cutoff, charger isolation, thermal rise and 4 h load test.
9. **Enclosure integration:** partition RF/digital/power zones; repeat noise-floor and antenna-pattern tests to quantify enclosure/display contamination.
10. **RF conducted calibration:** characterize every gain/filter/switch/pad state across frequency, input level and temperature; determine compression guard bands.
11. **Triaxial radiated calibration:** calibrate each axis and synthesized isotropy using a traceable field and rotation fixture.
12. **E-field conversion validation:** process blinded field levels, compare calculated E with reference, verify ER/TER and uncertainty propagation.
13. **Reference-instrument comparison:** colocate with calibrated Narda/Wavecontrol or equivalent; compare broadband vector result and band-summed selective result without assuming equality outside matched bandwidths.
14. **Indoor campaign:** survey coordinates, perform fast hotspot scan, execute 6-minute records at documented 1.5–2.0 m height, log environment/configuration and generate Kriging-ready data.

## 13. Validation and acceptance plan

Acceptance gates are:

- No sample loss at the chosen sustained I/Q rate for 6 h while FFT, GUI and logging run.
- Frequency tuning/clock error measured against traceable reference; 10 MHz option evaluated.
- VNA data for every RF state and stable switch truth table after ≥10,000 cycles.
- Receiver linearity/compression/noise-floor curves for every band/gain/pad state; overload alarm validated.
- Measured battery typical/peak power and ≥4 h operation with ≥15% residual energy.
- Per-axis AF/effective-height tables and repeatable mechanical geometry.
- Rotation/isotropy test and quantified sequential-axis bias under modulated/time-varying sources.
- End-to-end E-field comparison at multiple frequencies and levels; uncertainty budget approved before quantitative field claims.
- Campaign records include timestamp, coordinates, height, bands/RBW, switch/gain state, temperature, serial numbers, calibration versions and operator notes.
- TER software unit tests use independently calculated reference cases and the applicable ANE/ICNIRP limits.

## 14. Risks, limitations and mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Discrete array is not inherently isotropic | Direction-dependent E error | Radiated axis/rotation calibration; freeze fixture; upgrade to IsoLOG or simultaneous calibrated probe if unacceptable |
| Sequential axes see different bursts | Biased vector sum | Fast deterministic cycle, synchronized timestamps, repeated cycles over averaging window; simultaneous 3-RX upgrade |
| Receiver compression/intermodulation | False low/high readings | Filters, 10 dB pad, gain-state rules, overload detector and signal-generator validation |
| B1 filter misses 0.5 MHz at each edge | Incomplete filtered band claim | Label 902.5–927.5 guaranteed path; bypass edge scan or procure exact 902–928 filter |
| B3 SMT filter PCB parasitics | Ripple/loss | Controlled-impedance layout and measured S-parameters |
| Jetson/display self-emissions | Raised noise floor | Shield compartments, distance, cable ferrites where validated, before/after enclosure scans |
| Public prices/stock change and Colombia logistics | Cost/schedule | $500 contingency and $839 headroom; revalidate quotations before purchase |
| No accredited calibration in BOM | No traceability/compliance | Schedule external accredited calibration/reference comparison as separate institutional service |
| 6 GHz extension stops at 6.0/6.2 GHz | Cannot cover full 5.925–7.125 GHz | Upgrade receiver, both switches, filter and antenna calibration together |

## 15. Measurement class and final verdict

**Conservative class at delivery: FREQUENCY-SELECTIVE EXPOSURE MONITOR / RESEARCH PROTOTYPE.** Before completed calibration it is a spectrum-sensing prototype only. After successful end-to-end radiated calibration and uncertainty validation it can support screening measurements, band-resolved E-field estimates, hotspot identification and exposure mapping. It remains **not compliance-capable** unless an accredited/competent assessment demonstrates that the complete instrument and procedure meet every applicable Colombian and normative requirement.

The baseline is technically buildable and financially defensible. Its strongest attributes are complete mandatory frequency coverage, raw I/Q, strong software ecosystem, replaceable RF paths and budget headroom. Its dominant technical debt is the custom discrete antenna’s traceability. The first upgrade should therefore be calibrated antenna/probe instrumentation, not more AI compute.

## 16. Upgrade path

1. Replace the discrete array with Aaronia IsoLOG 3D Mobile 9060 PRO after verifying a usable calibrated antenna factor and its USB control on Jetson.
2. Add a calibrated broadband WPF8/Narda system as reference instrumentation, not as an SDR input.
3. Move to three simultaneous coherent receiver channels if sequential-axis error exceeds the uncertainty target.
4. Implement a PE42542/≥8 GHz receiver/filter/antenna chain for full Wi-Fi 6E/7.
5. Add a traceable 10 MHz reference and environmental sensors after amplitude-chain closure.
6. Replace developer-kit/carrier and allowance items with production MPNs after prototype qualification.

## 17. References

### Normative/local sources consulted

- ICNIRP, *Guidelines for Limiting Exposure to Electromagnetic Fields (100 kHz to 300 GHz)*, 2020; local `docs-RAG-RNI/ICNIRP_2020.pdf`.
- Colombia, Decreto 1370 de 2018; local `docs-RAG-RNI/Decreto_1370_de_2018.pdf`.
- ANE, Resolución 773 de 2023; local `docs-RAG-RNI/ANE_0773_2023.pdf`.
- ITU-T K.52 (08/2024), K.61 (10/2025), K.91 (01/2024), K.100 (08/2024), K.113 (07/2025); local normative PDFs.
- Repository `PROJECT_SCOPE.md`, `LIST_IOT_SERVICES.md`, `RNISensor.tex`, `references.bib`, and Martínez-González et al. indoor Kriging support document.

### Manufacturer and procurement sources

- [Ettus USRP B206mini-i product/purchase](https://www.ettus.com/all-products/USRP-B206mini-i/); [B200mini-i alternative](https://www.ettus.com/all-products/usrp-b200mini-i-2/).
- [NVIDIA Jetson Orin platform](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-orin/); [developer-kit guide](https://docs.nvidia.com/jetson/orin-nano-devkit/user-guide/index.html).
- [Waveshare 7inch HDMI LCD (C)](https://www.waveshare.com/7inch-hdmi-lcd-c.htm).
- [Taoglas TG.66.A113](https://www.taoglas.com/product/apex-tg-66-miniature-5g-4g-terminal/); [datasheet](https://www.taoglas.com/datasheets/TG.66.A113.pdf); [DigiKey price](https://www.digikey.com/en/product-highlight/t/taoglas/tg-66-5g-4g-antenna).
- [pSemi PE42442 datasheet](https://psemi.com/pdf/datasheets/pe42442ds.pdf); [DigiKey purchase](https://www.digikey.com/en/products/detail/psemi/PE42442A-Z/4747403).
- Mini-Circuits [B1](https://www.minicircuits.com/WebStore/dashboard.html?model=ZX75BP-915-S%2B), [B2](https://www.minicircuits.com/WebStore/dashboard.html?model=VBF-2450%2B), [B3](https://www.minicircuits.com/WebStore/dashboard.html?model=BFCN-3600%2B), [B4](https://www.minicircuits.com/WebStore/dashboard.html?model=VBFZ-5500-S%2B), and [10 dB pad](https://www.minicircuits.com/WebStore/dashboard.html?model=VAT-10A%2B).
- [Bioenno BLF-1215A](https://www.bioennopower.com/products/12v-15ah-lifepo4-battery-pvc); [BPC-1504DC charger](https://www.bioennopower.com/products/lithium-12v-4amp-lifepo4-battery-charger); [Pololu 5 V/9 A regulator](https://www.pololu.com/product/2866).
- [Aaronia IsoLOG 3D Mobile](https://aaronia.com/en/produkte/antennas/isolog-3d-mobile); [US price list](https://www.tequipment.net/Aaronia-pricelist/).
- [Narda NBM probes](https://www.narda-sts.com/en/products/emf-measuring-devices-and-solutions/nbm-probes/); [EF0691 datasheet](https://www.narda-sts.com/index.php?dl=1&eID=dumpFile&f=1304&t=f&token=bb5dc97e7304ccb419270f8a799d2cd8f87e4144).
- [Wavecontrol probes](https://www.wavecontrol.com/emf-products/electric-field-probes/); [WPF8 datasheet](https://www.wavecontrol.com/rfsafety/images/data-sheets/en/WPF8_Datasheet_EN.pdf).

All links and prices were accessed 2026-08-25. Purchase staff must re-check stock, export restrictions, shipping to Colombia, taxes and quotations immediately before ordering.
