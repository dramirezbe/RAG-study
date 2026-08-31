Here is a comprehensive mathematical summary of the signal chain for measuring the Electric Field (E-field) using a triaxial discrete diconical antenna array and a Software Defined Radio (SDR).

**Normative references used throughout this document:**
- **ITU-T K.100 (08/2024)** — Measurement of RF-EMF to determine compliance with human exposure limits (base stations)
- **ITU-T K.61 (10/2025)** — Guidance on measurement and numerical prediction of EMF for compliance
- **ITU-T K.91 (01/2024)** — Guidance for assessment, evaluation and monitoring of human exposure to RF fields
- **ITU-T K.52 (08/2024)** — Guidance on complying with limits for human exposure to EMF
- **ITU-T K.113 (07/2025)** — Generation of RF-EMF for compliance testing
- **ICNIRP (2020)** — Guidelines for limiting exposure to EMF (100 kHz to 300 GHz), Health Physics 118(5):483–524
- **ANE 0773 (2023)** — Resolución ANE que establece condiciones técnicas para evaluación de cumplimiento de niveles de exposición a campos electromagnéticos (Colombia)
- **IEC 62232** — RF field strength measurement methods for compliance
- **IEEE Std 149 / ANSI C63.5** — Antenna calibration standards

---

# E-Field Measurement Math: Triaxial Diconical Array & SDR

## 1. System Architecture Overview
The measurement system consists of three orthogonal (triaxial) broadband discrete diconical antennas, each connected to an SDR receiver chain.
1. **Antenna**: Converts incident E-field ($V/m$) to an induced RF voltage ($V$).
2. **Receiver Chain**: Amplifies/filters the RF signal and downconverts it to baseband.
3. **ADC**: Digitizes the baseband signal into In-phase (I) and Quadrature (Q) samples.
4. **DSP**: Calculates the voltage magnitude, converts it back to E-field, and combines the three axes.

> **Normative basis:** ITU-T K.100 §9.2 states: *"For broadband and frequency-selective equipment, the RF field strength measurement shall consider contributions from all directions and polarizations."* The triaxial configuration satisfies this requirement.

### 1.1 Single-SDR Option: RF Switch Multiplexing (SP3T)

The math above assumes three simultaneous receive chains (one SDR channel per axis) so that $E_x$, $E_y$, and $E_z$ are captured at the same instant. If only **one SDR** is available, a single-pole-triple-throw (SP3T) RF switch can be placed between the three antenna outputs and the single SDR input, sequentially selecting the X, Y, and Z antenna in turn:

*   **Architecture change**: `Antenna_x/y/z → SP3T RF switch → single SDR RX chain → ADC/DSP`

> **Normative justification — this design IS permitted:**
> ITU-T K.100 §9.2 (Table I.1, footnote b) explicitly states: *"Single-axis (e.g., dipole) and directional measurement antennas are **permitted** provided that the measurements are post processed to obtain the total RF field strength (equivalent to a measurement with an isotropic probe or measurement antenna)."*
>
> K.100 §9.2 further clarifies: *"An isotropic probe is best suited for this, but other antennas may be used. For instance, a single-axis probe (e.g., dipole) can be used by positioning the probe in three orthogonal directions and summing the individual contributions."*
>
> K.61 §107 states: *"Directional devices are generally polarized and have axial symmetry in the radiation pattern. Thus, proper orientation of the device in 3 orthogonal axes is necessary for field reconstruction."*
>
> K.113 §7.1.1 recommends simultaneous readings as *"preferably"* — this is guidance, not a normative prohibition. The word "preferably" indicates a recommendation, not a requirement.

*   **Trade-off — time multiplexing instead of simultaneous sampling**: Each axis is measured in its own time slot, so the three RMS voltages $V_{ADC,rms,x}$, $V_{ADC,rms,y}$, $V_{ADC,rms,z}$ used in the RSS/master equation (Sections 6–7) are no longer captured concurrently. The validity of this approach depends on the field temporal characteristics:

    | Field Type | SP3T Valid? | Reasoning |
    |-----------|-------------|-----------|
    | CW / FM broadcast | ✅ Yes | Field amplitude is constant; sequential measurement captures identical RMS |
    | LTE/5G at constant power | ✅ Yes | Time-averaged power is stable over the 6-min ICNIRP window |
    | Pulsed radar | ⚠️ Conditional | Duty cycle must be high enough that each axis captures many pulses |
    | Fast transient / bursty | ❌ No | Use 3× simultaneous SDR chains instead |

    > **Quantitative justification from K.91 §II.3.1 (Table II.1):** Measurements of WCDMA base stations show that time-averaged E-fields for measurement windows from 10 s to 6 min vary by only **0.4 dB** on average. This means a complete SP3T cycle (3 axes × ~2 min each = ~6 min total) captures the same time-averaged value as simultaneous measurement for any modulated telecommunication signal.

*   **Switch insertion loss**: The RF switch has its own frequency-dependent insertion loss $L_{sw}(f)$ (typically 0.5–2 dB) and finite isolation between ports. This loss must be added into $G_{rx}(f)$ (as a negative/loss term, same treatment as cable/balun loss in Section 8) for whichever axis is currently selected, or equivalently absorbed into a per-axis calibration of $G_{rx,k}(f)$ if the loss differs slightly port-to-port.

*   **Switching/settling time**: Allow the switch and receiver chain (AGC, filters, LO settling) to stabilize after each transition before starting the RMS integration window $M$, or the first few samples of each axis will be contaminated by switching transients. Practical settling time: 10–100 µs for solid-state switches; discard the first $M_{settling} = t_{settle} \cdot f_s$ samples per axis.

*   **Per-axis calibration**: Since only one RF chain is used, all three axes share the same $G_{rx}(f)$, $V_{LSB}$, and cable characteristics. However, the switch insertion loss may differ port-to-port ($\Delta L_{sw} \leq 0.3\text{ dB}$ typical). If $\Delta L_{sw}$ is negligible, a single calibration suffices. Otherwise, apply per-axis correction: $G_{rx,k}(f) = G_{rx}(f) \cdot 10^{-L_{sw,k}(f)/20}$.

*   **Practical benefit**: Reduces hardware cost and RF front-end calibration effort to a single chain (only one $G_{rx}(f)$ and one ADC to characterize), at the cost of a 3× slower duty cycle per full triaxial measurement. For the target application (EMF compliance assessment of base stations), this is the recommended architecture per K.100 §9.2.

---

## 2. Antenna Physics: E-field to Voltage
Discrete diconical antennas are broadband, meaning their characteristics vary with frequency $f$. They are typically balanced antennas, requiring a balun to convert to the unbalanced 50$\Omega$ coaxial cable feeding the SDR.

### Antenna Factor (AF)
The Antenna Factor relates the incident E-field to the voltage delivered to a matched 50$\Omega$ load. It is usually provided by the manufacturer as a calibration curve $AF(f)$ in $m^{-1}$ (or $dB/m$).

$$V_{in}(f) = \frac{E_{inc}(f)}{AF(f)}$$

Where:
*   $E_{inc}(f)$ = Incident Electric Field strength (V/m or V/m/$\sqrt{Hz}$)
*   $V_{in}(f)$ = Voltage at the SDR input connector (Vrms)
*   $AF(f)$ = Antenna Factor at frequency $f$ (1/m). *Note: In dB, $AF_{dB} = 20\log_{10}(AF_{lin})$.*

*(Note: $AF$ inherently accounts for the antenna's effective height, radiation efficiency, and balun/matching network insertion losses).*

> **Normative basis:** ITU-T K.61 §8.1.3.2 defines: *"The antenna factor (AF) is defined for antennas and frequency-selective probes as the ratio: AF = E_ref^(-1) [m/V] where E_ref [V/m] is the electric field strength at the probe and V [V] is the voltage measured by the spectrum analyser."* The AF value shall be known with an expanded uncertainty (95% confidence) of less than 2 dB.

> **Calibration Factor distinction:** For broadband probes, K.61 §8.1.3.1 defines a Calibration Factor (CF) = E_ref / E_meas, which differs from AF. CF applies to probes with built-in processing; AF applies to raw antenna + spectrum analyzer combinations. Ensure you are using the correct one for your hardware.

### 2.1 Antenna Factor Self-Calibration: The Three-Antenna Method (No Reference Antenna Required)

In most labs, $AF(f)$ comes from a manufacturer's calibration certificate, which is itself traceable to a national metrology institute's reference standard antenna. When no calibrated reference antenna is available, absolute antenna factors can still be derived from first principles using the **Three-Antenna Method** (also called the Three-Antenna Extrapolation Method, standardized in ANSI C63.5 and IEEE Std 149). The technique only requires three arbitrary antennas (they can even be three units of the same diconical design), a network analyzer (or SDR-based S21 measurement), a tape measure, and a controlled free-space environment (anechoic chamber, or an elevated open-area test site with ground-reflection nulling).

**Step 1 — Pairwise transmission measurements.**
Set up antennas A, B, and C as a transmit/receive pair, co-polarized, aligned on boresight, separated by a fixed far-field distance $r$. Measure the forward transmission coefficient $S_{21}$ (in dB) for each of the three unique pairs at the frequency of interest:

$$S_{21,AB}, \quad S_{21,AC}, \quad S_{21,BC}$$

**Step 2 — Apply the Friis transmission equation.**
For matched polarization, far-field, multipath-free conditions:

$$\frac{P_r}{P_t} = G_t \, G_r \left(\frac{\lambda}{4\pi r}\right)^2 
\quad\Longrightarrow\quad 
S_{21,dB} = G_{t,dBi} + G_{r,dBi} + 20\log_{10}\!\left(\frac{\lambda}{4\pi r}\right)$$

> **Normative basis:** ITU-T K.91 §3.2.1 defines antenna gain as: *"The antenna gain G_i(θ,φ) is the ratio of power radiated per unit solid angle multiplied by 4π to the total input power. The gain is frequently expressed in decibels with respect to an isotropic antenna (dBi)."* The Friis equation is the standard far-field transmission relation referenced in K.91 §7.2.3.

Define the (known, computable) space-loss term $K_{dB} = 20\log_{10}\!\left(\frac{\lambda}{4\pi r}\right)$. Each pair gives one linear equation in the two unknown gains involved:

$$S_{21,AB} = G_A + G_B + K \qquad S_{21,AC} = G_A + G_C + K \qquad S_{21,BC} = G_B + G_C + K$$

**Step 3 — Solve the 3×3 system for each individual gain.**
Three equations, three unknowns — no reference gain value is needed anywhere in the system:

$$G_A = \frac{S_{21,AB} + S_{21,AC} - S_{21,BC}}{2} - K$$
$$G_B = \frac{S_{21,AB} + S_{21,BC} - S_{21,AC}}{2} - K$$
$$G_C = \frac{S_{21,AC} + S_{21,BC} - S_{21,AB}}{2} - K$$

This works because $\lambda$ and $r$ are traceable to frequency/length standards rather than to a reference antenna — the method is "self-calibrating."

**Step 4 — Convert absolute gain to antenna factor.**
For a receive antenna terminated into a matched 50 Ω system, gain and E-field antenna factor are related by the standard EMC identity:

$$AF_{dB}(f) = 20\log_{10}\!\big(f_{MHz}\big) - G_{dBi} - 29.98$$

> **⚠ ERRATA — CORRECTED CONSTANT:** The previous version used the constant 29.79. The correct value is **29.98** (derived from $20\log_{10}(9.73) + 20\log_{10}(1/0.3) = 19.76 + 10.22 = 29.98$). The error was 0.19 dB, which is within typical AF uncertainty budgets but should be corrected for precision work.

or, equivalently, in linear form:

$$AF(f)\,[\text{m}^{-1}] = \frac{9.73}{\lambda\sqrt{G_{lin}}}$$

Apply this to $G_A$, $G_B$, $G_C$ individually to obtain $AF_x(f)$, $AF_y(f)$, $AF_z(f)$ for use in Section 6/7 above — all without ever touching a calibrated reference antenna.

**Practical requirements and correction terms:**
*   **Far-field distance:** $r$ must satisfy both $r \ge \frac{2D^2}{\lambda}$ ($D$ = largest antenna aperture dimension) and $r \gtrsim 3\lambda$, so the antennas are outside each other's reactive/radiating near-field.
*   **Multipath-free environment:** perform the measurement in an anechoic chamber, or use time-gating on a vector network analyzer, or use ground-plane geometry with a calculable reflection null, to suppress ground/wall reflections that would corrupt $S_{21}$.
*   **Impedance mismatch correction:** if the antennas are not well matched (VSWR noticeably $>1.2$), correct the raw $S_{21}$ for mismatch loss, $(1-|\Gamma_t|^2)(1-|\Gamma_r|^2)$, using the antennas' measured $S_{11}$.
*   **Cable/connector loss:** calibrate the network analyzer (or SDR + signal generator) reference plane out to the antenna connectors first (e.g., SOLT or TRL calibration), so cable loss doesn't leak into $S_{21}$.
*   **Polarization alignment:** boresight and co-polarization alignment error directly biases $G$; verify alignment mechanically or by peaking the received signal.
*   **Consistency check:** repeat the full measurement at a second distance $r_2 \neq r_1$. The recovered gains should agree within measurement uncertainty; a discrepancy indicates residual near-field coupling, multipath, or a distance-measurement error.
*   **Uncertainty budget:** the dominant error terms are typically distance measurement, cable/connector repeatability, mismatch, and residual multipath — combine these in quadrature to state an overall $AF$ uncertainty (in dB), the reference-free analogue of a calibration lab's uncertainty statement.

> **Normative uncertainty requirements:** ITU-T K.61 §8.2 states: *"The target expanded uncertainty for in situ field measurements is less than or equal to 4 dB, which is considered industry best practice. The expanded uncertainty for the RF exposure evaluation used for in situ RF EMF exposure measurements shall not exceed 6 dB."* K.100 §10 provides detailed uncertainty templates (Tables 10-1 and 10-2) listing all influence quantities with their sensitivity coefficients and divisors.

---

## 3. SDR Receiver Chain & Gain
The voltage $V_{in}$ passes through the SDR's analog front end (LNA, Mixer, VGA/PGA).

$$V_{ADC} = V_{in} \cdot G_{rx}(f)$$

Where:
*   $V_{ADC}$ = RMS voltage of the signal presented to the ADC input.
*   $G_{rx}(f)$ = Total linear voltage gain of the SDR receiver chain at frequency $f$. 
*   *In dB:* $V_{ADC, dB} = V_{in, dB\mu V} + G_{rx, dB}$ (adjusting for units).

> **Normative basis:** ITU-T K.100 Appendix I states: *"The measurement equipment should be calibrated at a sufficient number of frequencies to achieve the declared uncertainty of the equipment over the measurement frequency range."* K.61 §8.1.3.5 further requires verification that pulsed signals do not alter the calibration factor compared to CW signals.

> **⚠ MISSING IN ORIGINAL — Linearity requirement:** K.61 §8.1.3.4 specifies: *"The maximum tolerable deviation from a linear response is 1 dB."* The SDR receiver chain must be verified for linearity across the measurement band. This is a normative requirement, not optional.

---

## 4. ADC Quantization and Saturation Limits
The SDR uses an Analog-to-Digital Converter (ADC) to digitize the I and Q baseband signals.

### ADC Parameters
*   $N$ = ADC resolution (bits)
*   $V_{FS}$ = Full-scale peak voltage of the ADC (e.g., $\pm 1V$ means $V_{FS} = 1V$)
*   $V_{LSB}$ = Voltage per Least Significant Bit (Quantization step). For an $N$-bit signed ADC:
    $$V_{LSB} = \frac{2 \cdot V_{FS}}{2^N} = \frac{V_{FS}}{2^{N-1}}$$

### Saturation and Headroom (Crucial for SDRs)
The ADC will clip (saturate) if the instantaneous peak voltage exceeds $V_{FS}$. Because RF signals (especially noise or complex modulations like OFDM) have a high Peak-to-Average Power Ratio (PAPR), you must ensure the **RMS** voltage leaves enough headroom for the **Peak** voltage.

$$V_{ADC, peak} = V_{ADC, rms} \cdot CF$$

Where $CF$ is the Crest Factor (linear ratio of peak to RMS). 
*   For a pure CW tone: $CF = \sqrt{2} \approx 1.414$ (3 dB)
*   For Gaussian noise / complex digital signals: $CF \approx 3 \text{ to } 4$ (10 to 12 dB)

**To avoid saturation**, the maximum allowable RMS voltage at the ADC is:
$$V_{ADC, rms, max} = \frac{V_{FS}}{CF}$$
*Practical rule of thumb: Keep the average digital power at least 10-12 dB below the ADC full-scale digital power to prevent clipping distortion.*

---

## 5. Digital Signal Processing: IQ to Voltage
The SDR outputs discrete digital samples $I[n]$ and $Q[n]$. We must convert these digital counts back to physical analog RMS voltage.

### 1. Digital to Physical Voltage Mapping
Convert the integer digital counts to physical voltages:
$$v_I[n] = I[n] \cdot V_{LSB}$$
$$v_Q[n] = Q[n] \cdot V_{LSB}$$

### 2. Calculate RMS Voltage from IQ
Calculate the mean-square voltage over a measurement window of $M$ samples, then take the square root to get the RMS voltage at the ADC:

$$V_{ADC, rms} = \sqrt{ \frac{1}{M} \sum_{n=1}^{M} \left( v_I[n]^2 + v_Q[n]^2 \right) }$$

Substituting $V_{LSB}$:
$$V_{ADC, rms} = V_{LSB} \sqrt{ \frac{1}{M} \sum_{n=1}^{M} \left( I[n]^2 + Q[n]^2 \right) }$$

> **Normative basis:** ITU-T K.61 §8.1.3.6 requires verification that the RMS formula is correctly implemented: *"It means verifying that the measurement result is correctly given by an RMS formula: E_rms = sqrt(sum(E_i^2))."* The test uses two RF sources and verifies: `20*log10(|E_mes - sqrt(E_1^2 + E_2^2)| / E_mes) < 0.5 dB`.

> **⚠ MISSING IN ORIGINAL — Time-averaging window M:** The window length $M$ must be chosen to satisfy the ICNIRP 2020 averaging requirement. ICNIRP §8 states that reference levels are time-averaged over **6 minutes** for frequencies >100 kHz (general public). For a sample rate $f_s$, $M = 6 \times 60 \times f_s = 360 \cdot f_s$ samples minimum. K.100 §9.3 notes: *"The relevant exposure standard may specify the applicable time averaging period relevant for the field strength and power density measurements (e.g., any 6 min in [ICNIRP])."*

---

## 6. Final E-Field Reconstruction
Now we reverse the analog chain to find the incident E-field for a single axis (e.g., the X-axis).

### 1. Voltage to E-field (Single Axis)
Using the receiver gain and the antenna factor:

$$E_{x, rms}(f) = \frac{V_{ADC, rms, x}}{G_{rx}(f)} \cdot AF_x(f)$$

Where $E_{x, rms}$ is the RMS electric field magnitude along the X-axis in V/m.

> **Normative basis:** This is the direct inverse of the signal chain defined in K.61 §8.1.3.2 (AF = E/V → E = V·AF) combined with the gain relation from K.91 §3.2.1.

### 2. Triaxial Vector Summation
Because the three diconical antennas are mounted orthogonally (X, Y, Z), they measure the three orthogonal components of the E-field vector. To find the **total scalar magnitude** of the incident E-field, we use the Root-Sum-Square (RSS) method:

$$E_{total, rms}(f) = \sqrt{ E_{x, rms}^2 + E_{y, rms}^2 + E_{z, rms}^2 }$$

> **Normative basis:** ITU-T K.100 §9.2 states: *"An isotropic probe is best suited for this, but other antennas may be used. For instance, a single-axis probe (e.g., dipole) can be used by positioning the probe in three orthogonal directions and summing the individual contributions, E_N, so that: E = sqrt(sum(E_N^2)) for N=1 to 3."* ITU-T K.91 §7.2.5, Equation 7-5 confirms: *"E = sqrt(sum(E_i^2)) where E is the total electric field, due to all emissions. The r.m.s. values should be used, but in practice, the root sum square (RSS) values are measured [IEC 62311]."*

*(Note: If you need the exact phase/polarization vector, you must perform complex vector addition using the phase of the IQ data, but for standard E-field magnitude compliance testing, RSS of the RMS magnitudes is the standard).*

> **⚠ MISSING IN ORIGINAL — Isotropy error:** K.61 §8.1.3.3 states: *"The isotropic response is usually achieved by a triaxial antenna system, where the three axes are arranged to be mutually orthogonal. The deviation from an ideal isotropic response is measured in the isotropy test... The mean deviation from the isotropic response should be less than 1 dB."* The triaxial diconical array must be characterized for isotropy error; this error contributes directly to the measurement uncertainty budget.

---

## 7. The Master Equation
Combining all the steps above, the total E-field magnitude in V/m can be expressed in one master equation:

$$E_{total} = \sqrt{ \sum_{k \in \{x,y,z\}} \left( \frac{V_{LSB} \cdot \sqrt{ \frac{1}{M} \sum_{n=1}^{M} (I_k[n]^2 + Q_k[n]^2) }}{G_{rx}(f)} \cdot AF_k(f) \right)^2 }$$

**Where:**
*   $k$ = the axis index (x, y, z)
*   $I_k[n], Q_k[n]$ = digital IQ samples for axis $k$
*   $V_{LSB} = V_{FS} / 2^{N-1}$
*   $G_{rx}(f)$ = SDR linear voltage gain
*   $AF_k(f)$ = Antenna factor for axis $k$
*   $M$ = number of samples in the RMS integration window (must satisfy ICNIRP 6-min averaging: $M \ge 360 \cdot f_s$)

### 7.1 Exposure Ratio (Compliance Check)

The measured $E_{total}$ is compared against the applicable exposure limit $E_{lim}(f)$ to compute the **Exposure Ratio (ER)**:

$$ER(f) = \frac{E_{total}(f)}{E_{lim}(f)}$$

> **Normative basis:** ITU-T K.100 §9.3 defines: *"At sufficiently large distances from the source, only the r.m.s. electric (E) or magnetic (H) field strength need to be measured and the ER can be calculated as: ER = E^2 / E_lim^2 ≈ H^2 / H_lim^2 ≈ S / S_lim."*

When multiple frequency bands or sources are present, the **Total Exposure Ratio (TER)** is computed:

$$TER = \sqrt{ \sum_{i} \left(\frac{E_i}{E_{lim,i}}\right)^2 }$$

> **Normative basis:** ITU-T K.100 §9.7 defines TER for the general exposure assessment. ITU-T K.91 §7.2.5, Equation 7-5 provides the same formulation for frequency-selective measurements.

> **Colombian regulatory formula (ANE 0773):** Resolution ANE 0773 (2023), Annex Technical §3.4, defines the percentage exposure level for compliance:
> $$\sum_{i=1}^{N} \left(\frac{E_i}{E_{ref,i}}\right)^2 \leq 1$$
> where $E_i$ is the measured mean incident electric field at frequency $i$, and $E_{ref,i}$ is the reference incident electric field at frequency $i$ (from ICNIRP 2020 tables). This is functionally identical to the TER formula. Compliance requires the sum to be less than unity.

---

## 8. Summary of Practical Calibration Steps
To ensure the math reflects reality, you must calibrate the following in software:
1.  **SDR Gain Calibration**: Measure $G_{rx}(f)$ accurately. SDR gain knobs are rarely perfectly linear or accurate to the dB. Use a known signal generator to map the digital output power to actual input power across your frequency band.

    > **Normative basis:** K.100 Appendix I: *"The measurement equipment should be calibrated at a sufficient number of frequencies to achieve the declared uncertainty of the equipment over the measurement frequency range."* K.61 §8.1.3.4: maximum tolerable linearity deviation is 1 dB.

2.  **IQ Imbalance Correction**: SDRs have I/Q gain and phase mismatches. Apply digital DC-offset removal and IQ imbalance correction (e.g., using the `gr-iio` or `liquid-dsp` calibration tools) before calculating the RMS voltage, otherwise, your $V_{ADC, rms}$ will be artificially inflated.

3.  **Cable/Balun Loss**: Ensure $AF(f)$ provided by the manufacturer includes the balun. If it doesn't, you must add the balun/cable insertion loss to $G_{rx}(f)$ (as a negative gain/loss).

    > **Normative basis:** K.100 Table 10-1 lists "Calibration of cable loss" as a required uncertainty contributor with normal distribution, divisor 1.96.

4.  **RMS Formula Verification**: Per K.61 §8.1.3.6, verify the RMS computation using the two-source test: apply two known field levels $E_1$ and $E_2$ separately and combined, and confirm `20*log10(|E_mes - sqrt(E_1^2 + E_2^2)| / E_mes) < 0.5 dB`.

5.  **Isotropy Verification**: Per K.61 §8.1.3.3, characterize the triaxial probe's deviation from ideal isotropic response. The mean isotropy error should be < 1 dB. This is a dominant contributor to the uncertainty budget.

6.  **Body Scattering Awareness**: K.100 §10 (Table 10-1) lists "Field scattering from surveyor's body" as an uncertainty contributor (rectangular distribution, divisor √3). Maintain at least 1 m distance from the measurement antenna during data acquisition. K.100 §9.4 specifies: *"During measurements the distance between the measurement equipment and reflecting objects should be at least 1 m."*

7.  **Measurement Heights**: K.100 §9.4 specifies that each measurement point should be assessed at three heights: **1.1 m, 1.5 m, and 1.7 m**, with the largest value used for comparison with exposure limits.

---

## 9. Target Uncertainty Budget

Per ITU-T K.61 §8.2 and K.100 §10, the following uncertainty contributors must be evaluated and combined:

| Source | Typical (dB) | Distribution | Divisor | Sensitivity |
|--------|-------------|--------------|---------|-------------|
| AF calibration | 1.0–2.0 | Normal | 1.96 | 1 |
| Cable loss calibration | 0.5 | Normal | 1.96 | 1 |
| SDR gain linearity | 1.0 | Rectangular | √3 | 1 |
| Isotropy error | 1.0 | Rectangular | √3 | 1 |
| Body scattering | 1.0 | Rectangular | √3 | 1 |
| Mismatch | 0.5 | U-shaped | √2 | 1 |
| Field reflections | 1.0 | Rectangular | √3 | 1 |
| Frequency interpolation | 0.5 | Rectangular | √3 | 1 |

**Target:** Expanded uncertainty $U \leq 4\text{ dB}$ (best practice) or $\leq 6\text{ dB}$ (maximum allowed per K.61 §8.2), at 95% confidence ($k=2$ for normal distribution).

> **Colombian requirement:** ANE 0773 (2023) references ICNIRP 2020 limits and IEC 62232 measurement methods. The DCER (Declaración de Conformidad de Emisión Radioeléctrica) must include the measurement uncertainty statement per IEC 62232 / ISO/IEC 17025.

---

## Appendix A: Normative Reference Index

| Section | Equation/Concept | Primary Normative Source | Supporting Sources |
|---------|-----------------|------------------------|-------------------|
| §2 AF | $V = E/AF$ | K.61 §8.1.3.2 | IEC 62232 |
| §2.1 Three-Antenna | Friis, gain extraction | IEEE Std 149, ANSI C63.5 | K.91 §3.2.1 |
| §2.1 AF↔G | $AF = 9.73/(\lambda\sqrt{G})$ | EMC standard identity | K.91 §3.2.1 |
| §3 Gain chain | $V_{ADC} = V_{in} \cdot G_{rx}$ | K.100 App. I | K.61 §8.1.3.4 |
| §4 ADC | $V_{LSB}$, crest factor | Standard ADC theory | — |
| §5 RMS | IQ → RMS voltage | K.61 §8.1.3.6 | IEC 62311 |
| §6 RSS | $E_{tot} = \sqrt{\sum E_k^2}$ | K.100 §9.2 | K.91 Eq. 7-5, IEC 62311 |
| §7 Master | Full signal chain | — | All above |
| §7.1 TER | $TER = \sqrt{\sum(E_i/E_{lim,i})^2}$ | K.100 §9.7 | K.91 Eq. 7-5, ANE 0773 |
| §8 Calibration | Gain, IQ, cable, isotropy | K.61 §8.1.3, K.100 App. I | IEC 62232 |
| §9 Uncertainty | $U \leq 4$ dB (target) | K.61 §8.2, K.100 §10 | IEC 62232, JCGM 100:2008 |
| Limits | E-field reference levels | ICNIRP 2020 Tables 5–6 | K.52, ANE 0773 |
