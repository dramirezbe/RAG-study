# SDR-Based FM Spectrum Monitoring Framework — RAG-Ready Edition

## 1. Document Identity and Provenance

**Normalized source identifier:** `03SpectrumSensingFM0_RAG_ready`  
**Source document:** *SDR-Based Spectrum Monitoring System for FM Broadcast Compliance Assessment* (`03SpectrumSensingFM0_complete.pdf`)  
**Source coverage:** Pages 1–12 are the main framework source. Pages 13–28 are excluded. From pages 29–32, only generic measurement-uncertainty concepts and generic decision rules are included.  
**Document role:** Technical and metrological monitoring framework for retrieval-augmented generation (RAG), teaching, system design, and requirements analysis.  
**Authority status:** This document is not a statute, regulation, station authorization, calibration certificate, or jurisdiction-specific measurement procedure.

This edition normalizes the source into hardware-agnostic concepts. Statements inherited from the source describe a monitoring framework; they do not establish legal limits. Each deployment shall identify and preserve the authoritative regulatory source, its jurisdiction, edition or effective date, applicable station or service conditions, and the procedure used to interpret it.

The provenance of a retrieved statement should include this normalized document identifier, the relevant section, the original source page or page range, and the transformation status in Appendix C.

## 2. Purpose and Scope

This framework defines an end-to-end approach for monitoring FM broadcast spectrum using a generic software-defined radio (SDR) measurement chain. It addresses:

- system architecture and functional responsibilities;
- primary compliance measurands and secondary observables;
- calibration, traceability, and measurement uncertainty;
- capability classification based on demonstrated end-to-end performance;
- a node-level knowledge pipeline from complex I/Q observations to evidence;
- regulatory and metrological rule evaluation;
- structured, audit-ready reporting; and
- coordination of distributed monitoring nodes.

The intended audience includes RF engineers, regulatory and metrology personnel, developers of validated processing systems, system operators, and learners studying spectrum-monitoring workflows.

This framework does not prescribe a receiver model, computing platform, transport interface, fixed acquisition rate, or implementation-specific signal-processing algorithm. It does not itself establish jurisdiction-specific limits. It defines what must be measured, calibrated, validated, documented, and linked to authoritative rules.

## 3. Regulatory and Standards Context

The source identifies four distinct kinds of context. They shall not be conflated:

| Context | Role in this framework |
|---|---|
| FCC rules for FM broadcasting | Jurisdictional regulation in the United States when applicable to the monitored service and authorization. |
| ANE provisions for FM broadcasting | Jurisdictional regulation in Colombia when applicable to the monitored service and authorization. The exact governing instrument and current edition must be verified by the deployment. |
| ITU-R BS.412 | International technical context for FM sound broadcasting; it does not automatically replace applicable national law or license conditions. |
| ISO/IEC 17025 | Metrological context for laboratory competence, measurement traceability, calibration, and uncertainty practices. |
| GUM / JCGM 100 | Metrological context for evaluating and expressing measurement uncertainty. |
| `03SpectrumSensingFM0` | A monitoring framework and requirements source, not the authoritative origin of jurisdiction-specific legal limits. |

For every deployment, the governing regulatory basis shall be explicitly selected before evaluation. Rule metadata shall identify the jurisdiction, authority, instrument, provision, version or effective date, measurand, reference point, numerical limit, units, boundary convention, and any license-specific condition.

**Critical regulatory rule:** The applicable numerical limit shall be obtained from the governing jurisdictional regulatory source. No numerical tolerance or threshold in this normalized framework is universal.

## 4. System-Level Requirements

1. **Reconfigurability.** Monitoring objectives, processing parameters, channel plans, decision rules, and reporting logic should be changeable through controlled software configuration. A change that affects a reported result shall be version-controlled, documented, and revalidated.
2. **Technical adequacy and deployability.** Cost and scale may influence architecture, but they shall not override the performance, reliability, calibration, traceability, or uncertainty required for the intended measurement class.
3. **Remote operation and data transport.** The system shall support authorized transfer of measurement metadata, health information, derived products, and evidence. It shall define delivery cadence, buffering, reconnection behavior, retention, latency, and integrity controls according to the monitoring mission.
4. **Cybersecurity and access control.** Data in transit and at rest shall be protected under the system security policy. Administrative and operator actions, configuration changes, software changes, and remotely initiated operations shall be authenticated, authorized, and auditable.
5. **Multi-channel capacity.** Concurrent channel monitoring shall remain within the instantaneous bandwidth, tuning range, dynamic range, processing capacity, storage throughput, and sustained-loss constraints of the complete system. Benchmarks shall establish the validated operating envelope.
6. **Measurement integrity.** The complete chain—from antenna and RF front end through acquisition, processing, evaluation, storage, and reporting—shall preserve the integrity of reported measurands.
7. **Traceability.** Every result, decision, alert, and evidence item shall link to the acquisition record, measurement conditions, receiver configuration, time and frequency references, calibration state, processing version, uncertainty characterization, and governing rule.
8. **Change control.** Changes to hardware, firmware, software, calibration, reference sources, installation, or processing that can affect a measurand shall trigger impact assessment and, where required, revalidation and uncertainty re-evaluation.

## 5. Calibration and Traceability Hierarchy

The monitoring system shall implement a three-tier calibration hierarchy proportional to the regulatory significance of the measurand. Calibration records shall identify the calibrated item or chain, reference plane, method, standards, traceability chain, environmental conditions, results, uncertainty, validity period, and responsible organization.

### 5.1 Tier 1 — Primary Laboratory Calibration

Tier 1 is an initial and periodically renewed end-to-end calibration in a controlled laboratory environment, traceable to recognized national or international standards. It establishes absolute performance at a documented reference plane, normally the antenna input or another explicitly declared plane.

Tier 1 should characterize, as applicable:

- frequency-reference offset, accuracy, stability, and environmental sensitivity;
- received-level accuracy and gain-state behavior;
- frequency response and passband shape;
- noise figure and usable dynamic range;
- linearity, compression, and spurious-response behavior;
- antenna factor, feedline loss, and other corrections needed for field strength; and
- relevant processing-chain bias and repeatability.

Each calibrated node or measurement chain shall have a unique calibration certificate and machine-readable calibration record.

### 5.2 Tier 2 — Secondary Field Verification

Tier 2 is periodic in-situ verification against a stable internal or external reference. It checks whether field performance remains consistent with the Tier 1 state; it does not independently recreate absolute traceability unless its procedure and reference chain explicitly support that claim.

The procedure shall define check quantities, reference signals, warning and action limits, environmental conditions, cadence, acceptance criteria, and consequences of failure. A failed or expired verification shall suspend compliance use of affected measurands until the cause is resolved and validity is re-established. Screening use may continue only if explicitly allowed and clearly labeled.

### 5.3 Tier 3 — Relative Channel Calibration

Tier 3 controls relative gain and, when relevant, phase distortion across the observation bandwidth. It supports spectral comparison and channel-to-channel consistency where absolute level accuracy is unnecessary or is supplied separately by Tier 1.

Relative calibration may use validated equalization, reference features, or passband characterization. Its record shall identify the bandwidth, frequency span, configuration, correction data, method, date, and relationship to the governing Tier 1 state. Tier 3 alone does not establish absolute received power or field-strength traceability.

## 6. Hardware-Agnostic Functional Architecture

The conceptual signal and evidence flow is:

```text
FM antenna
→ generic RF/SDR front-end
→ complex I/Q acquisition
→ processing platform
→ DSP and spectrum-monitoring engine
→ compliance measurand estimation
→ regulatory/metrological evaluation
→ structured evidence storage
→ monitoring/reporting interface
```

The **FM antenna** couples the incident field into the measurement chain. Its orientation, polarization, installation, factor, and associated losses are material when field strength is reported.

The **generic RF/SDR front-end** selects and converts the observed spectrum into complex baseband. Fitness depends on demonstrated instantaneous bandwidth, dynamic range, frequency-reference accuracy and stability, noise figure, gain behavior, linearity, selectivity, and calibration support.

The **complex I/Q acquisition** function produces time-associated digital observations and configuration metadata. Acquisition settings shall be selected for the measurement purpose and validated for sustained, loss-aware operation; this framework sets no fixed sample rate.

The **processing platform and monitoring engine** perform validated conditioning, isolation, tracking, optional demodulation, measurand estimation, quality control, and provenance capture.

The **regulatory/metrological evaluation** function binds estimates and uncertainty to the authoritative rule and adopted decision procedure. The **structured evidence store** preserves reconstructable records, while the **monitoring/reporting interface** provides controlled review, trend analysis, alerts, and export.

## 7. FM Compliance Measurement Model

A measurand is a clearly defined physical or derived quantity intended for regulatory assessment. The applicable measurement procedure shall define the quantity, reference point, conditions, calculation, uncertainty, and rule linkage before measurement begins.

### 7.1 Primary Compliance Measurands

The framework defines seven primary measurands. Their capability class is determined independently for the deployed measurement chain and use case.

| Measurand | Symbol | Operational definition | Units | Estimation method/category | Calibration dependency | Reporting conditions | Regulatory hook |
|---|---:|---|---|---|---|---|---|
| Carrier frequency error | Δf_c | Difference between estimated channel center frequency and the assigned station frequency. | Hz | Spectral peak, centroid, or validated model-based center estimation referenced to the system frequency standard. | High: validated frequency-reference accuracy and stability, offset calibration, and drift characterization. | Periodic and event-triggered; include assigned frequency, observation interval, reference state, estimator, and uncertainty. Compliance reporting requires traceable frequency validation. | Assigned-frequency tolerance in the applicable regulation or station authorization. The applicable numerical limit shall be obtained from the governing jurisdictional regulatory source. |
| Calibrated received power | P_rx(B) | Power at a defined receiver reference plane, integrated over stated bandwidth B after gain-state and frequency-response correction. | dBm or dBµV | Calibrated power spectral density estimation and integration over the declared analysis bandwidth. | High: gain calibration, response correction, linearity validation, and documented reference plane. | Periodic or policy-defined; state B, reference plane, gain state, corrections, averaging, and uncertainty. | Site-, service-, license-, or policy-specific level criterion. The applicable numerical limit shall be obtained from the governing jurisdictional regulatory source. |
| Field strength | E | Electric field strength at the monitoring antenna location inferred from the calibrated receive chain and antenna characterization. | dBµV/m | Convert calibrated received level using antenna factor, feedline loss, front-end corrections, and the declared geometry and polarization conditions. | Very high: antenna factor and end-to-end chain calibration are required. | Periodic or procedure-defined; report location, height, orientation, polarization, reference plane, corrections, environment, averaging, and uncertainty. | Applicable field-strength, coordination, or service-limit rule adopted by the jurisdiction. The applicable numerical limit shall be obtained from the governing jurisdictional regulatory source. |
| Occupied bandwidth | B_occ | Minimum frequency interval containing the specified fraction β% of total integrated mean emission power. | Hz or kHz | Validated β%-power occupied-bandwidth measurement over the recorded spectrum. | Moderate to high: response flatness, analysis resolution, windowing, span adequacy, dynamic range, and interference control. | Periodic; state β, analysis span, resolution, window, integration/averaging interval, interference treatment, and uncertainty. | Occupied-bandwidth or spectral-containment provision in the governing regulation, authorization, or adopted procedure. Parameters and numerical limits come from that source. |
| Adjacent-channel / out-of-band emission level | L_adj, L_OOB | Emission level in declared adjacent or out-of-band regions, relative to an in-band reference or at a calibrated reference plane. | dBc, dBm, or other rule-defined level unit | Spectral integration or peak/envelope evaluation in rule-defined offset regions. | High: usable dynamic range, linearity, spectral response, gain calibration when absolute level is used, and proof that the receiver is not generating the observed product. | Periodic and event-triggered; state offset regions, detector, bandwidth, reference level, averaging, corrections, overload checks, and uncertainty. | Emission-mask or attenuation provision adopted by the governing authority. Regions, detectors, references, and numerical limits come from that source. |
| Peak deviation / multiplex-related indicator | Δf_max, P_MPX | Maximum instantaneous FM deviation and, where required, a multiplex-related modulation quantity derived from demodulated composite baseband. | kHz, dBr, or the metric defined by the adopted procedure | Validated FM demodulation followed by deviation estimation or multiplex-related measurement. | High: frequency-scale validity, demodulator linearity, deviation calibration, baseband response, and conditioning validation. | Windowed estimate with periodic summary; state observation window, estimator, baseband conditioning, reference, aggregation, and uncertainty. | Applicable deviation or multiplex-related provision in the governing regulation or adopted measurement procedure. The applicable numerical limit shall be obtained from that source. |
| Channel occupancy over time | O_T | Fraction of a declared observation interval during which a channel is classified as occupied under a documented detection rule. | % or dimensionless fraction | Threshold-, energy-, or feature-based detection over repeated windows with a declared revisit strategy. | Moderate: noise-floor and threshold validation, timebase, detection probability/false-alarm characterization, and decision-logic validation. | Rolling or study-defined summary; state interval, window and revisit schedule, threshold, missing-data treatment, detection rule, coverage, and uncertainty or confidence characterization. | Occupancy definition and decision criterion established by the governing monitoring policy; this framework supplies no universal occupancy threshold. |

### 7.2 Secondary Observables

Secondary observables provide diagnostic, identification, or evidentiary support. Examples include demodulated audio excerpts, composite-baseband or spectral snapshots, spectrograms, time-frequency visualizations, anomaly scores, and classifier outputs.

They shall not independently establish compliance unless the governing regulation or validated measurement procedure explicitly elevates them to that role. Their provenance, processing method, limitations, retention authority, and privacy implications shall be documented.

### 7.3 Carrier Frequency Error

Let the assigned carrier frequency be `f_assigned` and the calibrated estimate be `f_est`. The carrier frequency error is:

`Δf_c = f_est − f_assigned`.

The record shall preserve the sign as well as the magnitude. Regulatory evaluation requires a validated frequency reference, a defined estimator and observation interval, correction and drift state, uncertainty, and an authoritative assigned frequency and tolerance. An estimate lacking adequate reference validation may be useful for screening but shall not be promoted to compliance evidence.

### 7.4 Calibrated Received Power

`P_rx(B)` is defined only with respect to a stated reference plane and analysis bandwidth `B`. The estimate shall incorporate the applicable gain, loss, passband, and calibration corrections. Reports shall distinguish absolute power from relative spectral level and shall record overload, clipping, and dynamic-range checks. Received power at a monitoring site is not automatically transmitter power or field strength.

### 7.5 Field Strength

Field strength `E` is derived from a calibrated received level plus antenna and path-to-reference-plane corrections. Validity depends on antenna factor, feedline loss, installation geometry, polarization, environmental conditions, and end-to-end uncertainty. Without the necessary antenna characterization and traceability, the chain may report received level but not compliance-grade field strength.

### 7.6 Occupied Bandwidth

`B_occ` is the minimum interval containing a procedure-defined fraction `β%` of integrated mean emission power. The governing procedure must provide `β` and any detector, resolution, window, span, averaging, exclusion, and boundary rules. The implementation shall demonstrate that acquisition bandwidth, spectral response, dynamic range, and interference do not truncate or bias the estimate.

### 7.7 Adjacent-Channel / Out-of-Band Emission Level

`L_adj` or `L_OOB` compares energy in explicitly defined offset regions with a declared in-band reference, or reports calibrated absolute level at a reference plane. Evaluation shall use the bandwidths, detector, offsets, attenuation reference, and limits specified by the governing rule. Receiver-generated distortion, overload, leakage, and insufficient dynamic range must be excluded or quantified before assigning a compliance-grade result.

### 7.8 Peak Deviation / Multiplex-Related Indicator

`Δf_max` and `P_MPX` are modulation-related quantities derived from a validated per-channel baseband path. The monitoring procedure shall define the baseband response, calibration, time window, peak or statistical estimator, multiplex weighting or reference, and treatment of transients. The precise multiplex metric and limit are jurisdiction- or procedure-dependent.

### 7.9 Channel Occupancy Over Time

For a declared observation interval `T`, occupancy `O_T` is the proportion of valid observation windows classified as occupied. The result is meaningful only with its detection threshold or model, noise-floor method, window length, revisit schedule, coverage, missing-data policy, and validation evidence. Occupancy is a policy-defined monitoring quantity and shall not be equated automatically with licensed operation, interference, or compliance.

## 8. Measurement Capability Classification

Capability shall be classified per measurand, configuration, operating range, environment, and intended regulatory use. A platform label alone cannot establish capability.

- **Compliance-Grade:** The complete measurement chain, calibration, validation, processing implementation, uncertainty budget, and reporting procedure have demonstrated fitness for the applicable regulatory tolerance and decision rule. The result may support formal compliance evaluation within the validated scope.
- **Screening-Grade:** The result supports anomaly detection, situational awareness, event screening, or preliminary assessment. It shall not be the sole basis for a formal adverse determination without independent compliance-grade corroboration.
- **Conditional Compliance-Grade:** The result can become compliance-grade only when explicitly documented preconditions have been implemented and independently validated. Until then, it is treated and labeled as screening-grade. Preconditions are measurement-chain requirements, not assumptions tied to a particular receiver model.
- **Unsupported:** The complete deployed chain cannot reliably estimate the measurand for the intended conditions. A numerical output may still exist but is not metrologically meaningful and shall not be reported as a valid measurement.

Classification evidence shall include the validated operating envelope, complete-chain configuration, applicable calibration tiers and record identifiers, uncertainty or screening disclaimer, processing version and method, environmental constraints, and any unmet preconditions. Screening records shall be clearly marked **FOR SCREENING ONLY** and excluded from formal compliance summaries unless a documented, authorized procedure supplies independent validation.

Classification shall be reviewed after material configuration, installation, calibration, environmental, firmware, software, or algorithm changes.

## 9. Measurement Uncertainty Requirements

Every compliance-grade measurand shall have a documented, itemized uncertainty budget consistent with GUM/JCGM principles and traceable to the calibration hierarchy. It shall identify the measurement model, input quantities, corrections, uncertainty contributors, evaluation type, assumed probability distributions, standard uncertainties, sensitivity coefficients where applicable, degrees of freedom where relevant, correlations, combined standard uncertainty, coverage factor, expanded uncertainty, and known limitations.

The expanded uncertainty shall accompany the measured value in compliance records and shall be used according to the selected decision rule. Screening-grade outputs shall include either an appropriate uncertainty characterization or an explicit statement that compliance-grade uncertainty has not been established.

The uncertainty model shall cover material contributions from the complete chain, which may include frequency and time references, gain and loss corrections, antenna characterization, linearity, passband response, noise, environmental variation, acquisition behavior, processing bias, estimator repeatability, and resolution effects. Contributors shall not be omitted merely because they originate in software.

Appendix A defines the permitted generic uncertainty concepts. Appendix B defines generic decision rules. Jurisdictional or contractual procedures may require a different or more specific method; where they do, that authoritative procedure governs and shall be cited.

## 10. Node-Level DSP Knowledge Pipeline

The preserved domain-knowledge pipeline is:

```text
Raw I/Q
→ Wideband Acquisition and Conditioning
→ Channelization, Isolation and Tracking
→ Optional Per-Channel Demodulation and Identification
→ Compliance Measurand Estimation
→ Rule Evaluation and Alerting
→ Reporting and Evidence Archiving
```

This sequence defines responsibilities and information flow, not implementation-specific algorithms.

1. **Wideband Acquisition and Conditioning** acquires complex samples and binds them to timing, configuration, loss/quality, and calibration metadata. It prepares observations for analysis while preserving the unmodified or integrity-referenced source evidence required by policy.
2. **Channelization, Isolation and Tracking** surveys the monitored span, detects candidate FM emissions, isolates channels, follows their spectral position, and characterizes persistence. Detection, isolation, and tracking methods must be validated within the intended signal and interference conditions.
3. **Optional Per-Channel Demodulation and Identification** derives composite baseband or other channel products needed for modulation measurands and optional station attribution. Identification and classification remain secondary unless an authoritative procedure states otherwise.
4. **Compliance Measurand Estimation** produces the operationally defined quantities in Section 7 and binds each estimate to method, configuration, observation interval, calibration state, quality flags, and uncertainty.
5. **Rule Evaluation and Alerting** selects the applicable authoritative rule, applies the documented decision rule, assigns a result such as compliant, non-compliant, uncertain, screening-only, or invalid, and records the complete decision context.
6. **Reporting and Evidence Archiving** persists results, uncertainty, decisions, provenance, supporting spectral or time-frequency products, selected signal excerpts where authorized, alerts, and integrity information for retrieval and audit.

## 11. Regulatory Rule Evaluation

Rule evaluation shall separate three objects:

1. the measured estimate and its conditions;
2. the metrological result, including uncertainty and validity; and
3. the governing regulatory criterion and decision rule.

Before evaluation, the system shall resolve the correct jurisdiction, service, station authorization, measurand definition, reference point, limit, units, detector, bandwidth, averaging interval, boundary convention, and effective date. The applicable numerical limit shall be obtained from the governing jurisdictional regulatory source.

The adopted decision rule shall be selected before the measurement cycle, documented in the procedure, and stored with each determination. A change between cycles shall create a configuration-change record and may require re-evaluation. Generic options are defined in Appendix B; the framework assigns no universal default rule to any measurand.

An evaluation shall explicitly distinguish:

- **compliant:** the result satisfies the selected acceptance condition;
- **non-compliant:** the result satisfies the selected rejection condition;
- **uncertain:** available evidence does not support either conclusive acceptance or rejection under the selected uncertainty-aware rule;
- **screening-only:** the estimate is informative but lacks compliance-grade capability; and
- **invalid/unsupported:** required validity conditions are absent or failed.

Uncertain outcomes shall follow a documented, authorized procedure appropriate to the jurisdiction and measurand. This framework intentionally does not prescribe a table-specific escalation sequence or fixed escalation time.

## 12. Reporting, Evidence and Provenance

Each reported measurand or decision shall preserve, as applicable:

- unique record, acquisition, node, site, and station identifiers;
- timestamp, time-reference state, observation interval, and temporal coverage;
- center frequency, observation bandwidth, acquisition parameters, and data-quality or loss flags;
- antenna configuration, reference plane, gain state, and relevant RF-chain configuration;
- calibration tier, status, validity interval, certificate or record identifier, and applied corrections;
- measurand symbol, operational definition, value, units, estimator, processing parameters, and averaging interval;
- uncertainty budget identifier, combined and expanded uncertainty, coverage factor, correlations, and limitations;
- capability class and validated-scope reference;
- governing jurisdiction, regulatory source and version, provision, applicable limit, and rule-resolution evidence;
- decision rule, boundary treatment, outcome, and alert context;
- software, firmware, configuration, model, and method versions;
- evidence artifact identifiers, integrity references, retention state, and access controls; and
- change, operator-action, review, and export audit records.

Evidence may include integrity-referenced raw I/Q excerpts, calibrated spectral estimates, spectrograms, baseband products, uncertainty budgets, health records, and decision traces when justified and authorized. Retention shall balance audit needs with storage, security, privacy, and legal requirements. A report shall clearly distinguish measured facts, derived quantities, screening indications, regulatory interpretations, and final authorized determinations.

## 13. Distributed / Multi-Node Monitoring

A distributed deployment shall be treated as one coordinated measurement system whenever it produces array-level conclusions. Local results may be computed independently, but fusion requires a common calibration, timing, reference, health, and provenance framework.

**Inter-node calibration.** The array shall characterize residual differences for every jointly used measurand, including relevant frequency offset, gain bias, passband deviation, timestamp offset, and site-dependent corrections. Verification may use a shared reference, transportable calibrated source, or controlled over-the-air reference. Only nodes within declared acceptance criteria are eligible for fusion.

**Timing alignment.** Common-event, occupancy, and corroboration analysis shall use a documented time base and common epoch/window definitions. Timestamp uncertainty, synchronization status, holdover behavior, and allowable skew shall be recorded. A node outside timing tolerance may continue eligible local functions but shall be excluded from time-critical fusion.

**Frequency-reference coordination.** The reference architecture shall document sources, traceability, distribution or derivation, stability, fault detection, holdover, and loss-of-reference behavior. Measurements taken during an invalid reference state shall be flagged and downgraded or rejected according to their measurand-specific impact.

**Health coordination.** Health assessment should cover acquisition continuity, sample loss, gain state, clipping, DC or I/Q impairments, thermal state, storage, communications, calibration validity, synchronization, and reference state. At minimum, nodes should be classified as operational for compliance use, operational for screening only, degraded, or unavailable.

**Cross-node consistency.** Overlapping results shall be compared only after relevant calibration, antenna, site, geometry, and propagation effects are considered. Persistent disagreement outside the expected uncertainty shall lead to exclusion of an outlier, an uncertain classification, or authorized review; it shall not be silently averaged away.

**Fusion rules.** A documented array rule shall define node eligibility, fusible and node-local measurands, weights based on calibration/uncertainty/health/geometry, corroboration requirements, boundary conditions, and treatment of conflicts or missing nodes. The appropriate fusion logic depends on the measurand and governing procedure; this framework does not establish a universal node count or voting threshold.

**Array-level traceability.** Every fused result shall link to participating node records, calibration and synchronization states, health states, local estimates and uncertainties, corrections, weights, consistency checks, fusion method/version, governing rule, and decision outcome.

**Degraded operation.** Loss of a node, calibration validity, common reference, communications, or timing alignment need not stop all monitoring. The system shall automatically restrict the measurement classes and fusion claims that remain valid, while clearly labeling screening, local-only, or evidence-collection operation.

## Appendix A — Measurement Uncertainty

### A.1 Type A uncertainty

Type A uncertainty is evaluated statistically from repeated observations under defined repeatability conditions. If `x_i` are `n` independent observations, `x̄` is their mean, and `s` is their experimental standard deviation, the standard uncertainty of the mean is:

```text
s = sqrt[ (1 / (n − 1)) Σ(x_i − x̄)² ]
u_A = s / sqrt(n)
```

The observation count, independence assumptions, repeatability conditions, exclusions, and statistical sufficiency shall be documented. Applicable contributors can include noise, environmental fluctuation, acquisition variation, and estimator repeatability.

### A.2 Type B uncertainty

Type B uncertainty is evaluated using information other than the statistical analysis of the current repeated observations. Sources can include calibration certificates, reference-standard uncertainty, prior data, specifications, physical models, and documented engineering judgment.

For a contributor bounded by `±a`, a standard uncertainty may be assigned from a justified distribution, for example:

```text
rectangular: u_B = a / sqrt(3)
triangular:  u_B = a / sqrt(6)
normal with stated expanded interval at k = 2: u_B = a / 2
```

The source, bounds, distribution, divisor, sensitivity to the measurand, and justification shall be recorded for each contributor.

### A.3 Combined standard uncertainty and correlations

For mutually independent standard-uncertainty components `u_i`, the combined standard uncertainty is obtained by root-sum-square:

```text
u_c = sqrt(Σ u_i²)
```

When contributors are correlated, the covariance terms shall be included:

```text
u_c² = Σ u_i² + 2 ΣΣ r(u_i, u_j) u_i u_j, for j > i
```

Here `r(u_i, u_j)` is the estimated correlation coefficient. Shared physical causes, shared calibration data, common corrections, or common environmental influences should prompt a correlation assessment. Significant correlations and their estimates shall be documented. An assumption of zero correlation shall be justified and reported as a limitation when correlation is unknown.

### A.4 Expanded uncertainty

Expanded uncertainty is:

```text
U = k · u_c
```

The coverage factor `k`, coverage interpretation, distributional assumptions, and any effective-degrees-of-freedom method shall be stated. A value of `k` shall not be inferred solely from this framework; it shall be selected and justified under the applicable metrological procedure.

### A.5 Periodic re-evaluation

The uncertainty budget shall be re-evaluated when calibration is performed or renewed; a material component, antenna, feedline, installation, reference, or configuration changes; software or firmware affecting the estimate changes; field verification identifies material drift; environmental conditions materially change; validation reveals bias or changed repeatability; or the governing method or decision rule changes.

The archived re-evaluation shall identify its trigger, date, affected measurands, prior and revised budgets, reviewer, and consequence for capability classification. If uncertainty no longer supports the selected rule or required capability, the measurand shall be downgraded, suspended, or revalidated under documented authority.

## Appendix B — Generic Decision Rules

Let `x` be a calibrated estimate, `x_ref` the regulatory reference value, `T` the permissible absolute departure from that reference, and `U` the expanded measurement uncertainty. `T` and its semantics must come from the governing jurisdictional source or authorized procedure.

### B.1 Simple threshold decision rule

```text
COMPLIANT:     |x − x_ref| ≤ T
NON-COMPLIANT: |x − x_ref| > T
```

This rule ignores measurement uncertainty. It should be used only when the governing authority explicitly requires it or when an authorized procedure demonstrates that uncertainty is negligible for the intended decision. The criterion for “negligible” shall come from that procedure, not from this framework.

### B.2 Conservative guard-band decision rule

```text
COMPLIANT:     |x − x_ref| + U ≤ T
NON-COMPLIANT: |x − x_ref| − U > T
UNCERTAIN:     otherwise
```

This rule reduces false-acceptance risk by requiring the upper uncertainty bound to remain inside the permitted interval for acceptance. The uncertain zone requires handling under a documented, authorized procedure.

### B.3 Shared-risk decision rule

Define an acceptance interval and rejection region:

```text
A = [x_ref − (T − U), x_ref + (T − U)]
R = (−∞, x_ref − (T + U)) ∪ (x_ref + (T + U), ∞)

COMPLIANT:     x ∈ A
NON-COMPLIANT: x ∈ R
UNCERTAIN:     x ∉ A ∪ R
```

The exact inclusion of boundary points shall be declared before evaluation. This rule is appropriate only when the authorized decision procedure treats false acceptance and false rejection as comparable risks. It does not by itself prove that the risks are numerically equal; that claim requires a stated probabilistic model.

For every rule, store the rule identifier/version, input value and units, `x_ref`, `T`, `U`, boundary convention, outcome, governing authority, and any review or override record.

## Appendix C — Transformation Log

| Source page(s) | Original topic | Action | Rationale |
|---|---|---|---|
| 1 | Purpose, audience, framework role, and regulatory/metrological context | RETAINED | Establishes scope and separates monitoring, regulatory, international-technical, and metrological roles. |
| 1, 5–6 | Embedded example tolerances and compliance references | GENERALIZED | Numeric limits lacking explicit jurisdictional authority were removed. Limits must be resolved from the governing jurisdictional source. |
| 2 | System-level requirements | RETAINED | Preserves reconfigurability, deployability, remote operation, security, capacity validation, integrity, and traceability. |
| 2–3 | Three-tier calibration hierarchy | RETAINED | Preserves laboratory calibration, field verification, and relative channel calibration while expressing them generically. |
| 3–4 | Hardware-specific architecture and original Figure 1 | GENERALIZED | Replaced by a text-only antenna-to-evidence architecture. References to HackRF, Dragonboard, Raspberry Pi/RPi, GNU Radio, USB, named SDR platforms, and fixed sample rates such as 20 MS/s were removed from normative content. The original figure was not reproduced. |
| 5–6 | Seven primary measurands and secondary observables | RETAINED | Preserves symbols, definitions, units, estimation categories, calibration dependencies, reporting conditions, and regulatory hooks. |
| 7 | Capability classification and metadata | GENERALIZED | Preserves Compliance-Grade, Screening-Grade, Conditional Compliance-Grade, and Unsupported based on demonstrated complete-chain capability; removes device-linked qualification and Table 3 dependency. |
| 8–10 | Node-level processing pipeline | RETAINED | Preserves domain-knowledge stages and responsibilities without implementing algorithms or device transport/configuration details. |
| 11–12 | Distributed monitoring | GENERALIZED | Preserves inter-node calibration, timing, reference, health, consistency, fusion, traceability, and degraded operation without platform-specific assumptions or a universal voting rule. |
| 13–28 | Implementation-specific algorithms, hardware analysis, and recommendations | REMOVED | Entire range excluded by scope; no content was used. |
| 29–30 | Type A, Type B, combined, correlated, expanded uncertainty, and re-evaluation | APPENDED | Included only generic metrological concepts; source-specific numeric recommendations and platform examples were not made normative. |
| 30–32 | Simple threshold, conservative guard-band, and shared-risk rules | APPENDED | Preserves generic mathematical rules while removing Table 4-specific defaults and handling procedures. |
| 31–32 | Table 4 escalation and fixed 72-hour policy | REMOVED | Operational escalation workflow and fixed timing are outside the permitted generic decision-rule scope. |

