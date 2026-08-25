# RNI Component Options

**Project:** SDR-Based RNI Human Exposure Monitoring System  
**Research/access date:** 2026-08-25  
**Currency:** USD unless stated otherwise

Prices are one-unit web prices observed on the access date, exclusive of tax, freight, Colombian import charges, and calibration. `PRICE NOT VERIFIED` means that no defensible public current price was found. A nominal ADC bit count is not a calibrated dynamic-range specification.

## SDR

| Manufacturer / MPN | Verified specifications | Advantages | Limitations and suitability | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| Ettus Research **789155-01, USRP B206mini-i** | 70 MHz–6 GHz; AD9364 12-bit converters; up to 56 MHz instantaneous BW; USB 3 Type-C; 10 MHz and PPS inputs; UHD 4.9+; full duplex | Covers all four baseline bands; mature UHD/GNU Radio; external reference; enclosure and cable included | Not a field-strength meter; absolute amplitude, temperature response, compression and path uncertainty require calibration | **$1,820**, orderable from Ettus | [Manufacturer/purchase](https://www.ettus.com/all-products/USRP-B206mini-i/) | **SELECTED**; adequate spectrum sensing and calibratable research E-field chain |
| Ettus Research **785887-01, USRP B200mini-i** | 70 MHz–6 GHz; 12-bit AD9364; 56 MHz BW; USB 3; 10 MHz/PPS | Same coverage and ecosystem | $85 cheaper but manufacturer identifies B206mini-i as newer; older Spartan-6 capacity | **$1,735**, orderable | [Manufacturer/purchase](https://www.ettus.com/all-products/usrp-b200mini-i-2/) | ALTERNATIVE; rejected in favor of current successor |
| Great Scott Gadgets **HackRF One** | 1 MHz–6 GHz; 8-bit I/Q; 2–20 MS/s; USB 2; half duplex; clock I/O | Lowest-cost complete-band survey receiver; GNU Radio/Linux support | Least headroom and USB throughput; more demanding overload management; not calibrated | Current authorized-reseller price **PRICE NOT VERIFIED** | [Manufacturer](https://greatscottgadgets.com/hackrf/one/), [manual](https://hackrf.readthedocs.io/en/stable/hackrf_one.html) | ALTERNATIVE for spectrum occupancy only; not selected for metrology baseline |
| Analog Devices **ADALM-PLUTO** | 325 MHz–3.8 GHz; 12-bit; 20 MHz channel BW; 61.44 MSPS converters; USB 2; GNU Radio/libiio | Low cost; good teaching and B1–B3 development | Cannot cover mandatory 5.15–5.85 GHz; advertised-range hacks are unsupported for a defensible design | **PRICE NOT VERIFIED** | [Manufacturer](https://www.analog.com/en/resources/evaluation-hardware-and-software/evaluation-boards-kits/adalm-pluto.html), [specs](https://wiki.analog.com/university/tools/pluto/devs/specs) | REJECTED for baseline coverage |
| Lime Microsystems **LimeSDR Mini 2.0** | 10 MHz–3.5 GHz; 12-bit; LMS7002M; USB 3 form factor | Open ecosystem and good value | Does not cover 3.5–3.7 GHz fully or B4 | **PRICE NOT VERIFIED** | [Manufacturer](https://limemicro.com/sdr/limesdr-mini-2-0/), [store](https://store.limemicro.com/products/limesdr-mini-2-0) | REJECTED for baseline coverage |

All SDRs are suitable for spectrum sensing within their stated range. None is compliance-capable by itself. Only a calibrated end-to-end assembly with a documented uncertainty budget can estimate E [V/m].

## Jetson

| Manufacturer / MPN | Specifications | Advantages | Limitations | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| NVIDIA **945-13766-0000-000, Orin Nano Super Developer Kit 8GB** | 6-core Cortex-A78AE; 1024 CUDA/32 Tensor cores; 8 GB LPDDR5; 67 sparse INT8 TOPS; 7/15/25 W; 4× USB 3.2; M.2 NVMe; DP; 100×79×21 mm | Lowest-cost kit with ample FFT/PSD, logging, GUI and moderate inference margin | Developer kit, not production module; GNU Radio/UHD on aarch64 must be bench-qualified; 8 GB constrains large training jobs | **$249** advertised by NVIDIA; availability via partners | [Product](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-orin/), [guide](https://docs.nvidia.com/jetson/orin-nano-devkit/user-guide/index.html) | **SELECTED**, run 15 W mode |
| NVIDIA **Orin NX 8GB module** | 6-core A78AE; 1024 CUDA cores; 70–117 sparse TOPS depending mode; 10–40 W | Greater inference/compute margin and DLA | Module needs carrier and thermal integration; more power and cost; unnecessary for the stated acquisition rate | NVIDIA FAQ lists **$649 at 1KU+**, not a one-off kit price | [Specifications](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-orin/), [power modes](https://developer.nvidia.com/blog/nvidia-jetpack-6-2-brings-super-mode-to-nvidia-jetson-orin-nano-and-jetson-orin-nx-modules/) | ALTERNATIVE for heavier ML, not baseline |
| NVIDIA **AGX Orin Developer Kit** | Up to 275 TOPS; large memory and I/O; high-power platform | Maximum margin | $3,499 class and high power/size; consumes project budget without measurement benefit | **$3,499** NVIDIA MSRP | [NVIDIA FAQ](https://developer.nvidia.com/embedded/faq) | REJECTED |

JetPack 6.2+ supplies CUDA; PyTorch is available in NVIDIA’s Jetson ecosystem. GNU Radio/UHD feasibility is technically strong but actual selected-version builds and sustained USB capture are acceptance tests, not assumed facts.

## Display

| Manufacturer / MPN | Specifications | Advantages | Limitations | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| Waveshare **SKU 11199, 7inch HDMI LCD (C)** | 7-inch IPS; 1024×600; HDMI; USB 5-point capacitive touch; 170° | Documented product, manufacturer resources, low cost | Orin Nano kit has DisplayPort, requiring active/passive DP-to-HDMI adapter as qualified; mounting/power current not numerically verified | **$43.99**, available | [Manufacturer/purchase](https://www.waveshare.com/7inch-hdmi-lcd-c.htm), [wiki](https://www.waveshare.com/wiki/7inch_HDMI_LCD_(C)) | **SELECTED** |
| Hosyond **H085 / listing-dependent** | Candidate listing claims 7-inch IPS, 1024×600, HDMI, USB/Type-C touch/power and generic Linux support | Low cost and matches conceptual image | Exact conceptual-image MPN, mounting drawing, consumption and Jetson-Orin validation not established; marketplace listing churn | $49.99 marketplace listing observed; authoritative availability **NOT VERIFIED** | [Manual mirror](https://manuals.plus/asin/B0FJY6SWQ3.pdf) | REJECTED due traceability gap |
| Hiwonder **21090088** | 7-inch, 1024×600, HDMI/USB, Jetson Nano stated | Explicit Jetson-family marketing; kit | More expensive and Orin Nano DP adapter still required | **$69.99**, 988 stated in stock | [Manufacturer/purchase](https://www.hiwonder.com/products/7-inch-hd-touch-scree) | ALTERNATIVE |

## Battery and power

| Manufacturer / MPN | Specifications | Advantages | Limitations | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| Bioenno Power **BLF-1215A** | 12 V nominal, 15 Ah, 180 Wh LiFePO4, internal protection/Powerpole | Safer cycle chemistry than hobby LiPo; protected pack; enough for a 6 h campaign at modeled load | External charger; actual delivered capacity and low-voltage cutoff must be acceptance-tested | **$149.99**, available | [Battery/purchase](https://www.bioennopower.com/products/12v-15ah-lifepo4-battery-pvc) | **SELECTED** |
| Bioenno **BPC-1504DC** | 14.6 V, 4 A LiFePO4 charger | Matched branded charger | No validated seamless UPS/power-path function | **$24.99**, available | [Charger/purchase](https://www.bioennopower.com/products/lithium-12v-4amp-lifepo4-battery-charger) | **SELECTED** |
| Jauch **LFP 1220 12V 20AH** | 12.8 V, 20 Ah LiFePO4 | More runtime and authorized-distributor traceability | Heavier; public price not captured | **PRICE NOT VERIFIED**, DigiKey listing | [Purchase](https://www.digikey.com/en/products/detail/jauch-quartz/LFP-1220-12V-20AH/27564966) | ALTERNATIVE for >6 h |
| 4S hobby LiPo pack | Nominal 14.8 V; many capacities | Cheap/light/high current | Pack/BMS/charger/configuration ambiguity and higher integration risk | **PRICE NOT VERIFIED** | NOT VERIFIED | REJECTED |

## RF switches

| Manufacturer / MPN | Specifications | Advantages | Limitations | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| pSemi **PE42442A-Z** | Absorptive SP4T; 30 MHz–6 GHz; 0.85–2.35 dB IL; 32–67 dB isolation; IIP3 58 dBm; 2.3–5.5 V; 0.255 μs | Covers baseline; inexpensive; direct GPIO logic possible; terminated ports | QFN PCB design, shielding and calibration required; worst-case loss near upper band | **$5.59**, thousands reported at authorized distributors | [Product catalog](https://psemi.com/products/rf-switches/rf-switches-all/), [datasheet](https://psemi.com/pdf/datasheets/pe42442ds.pdf), [DigiKey](https://www.digikey.com/en/products/detail/psemi/PE42442A-Z/4747403) | **SELECTED**, two-stage topology plus spares |
| pSemi **PE42542** | Absorptive SP4T; 9 kHz–18 GHz; 0.7–3.9 dB; 27–90 dB isolation; 2.3–5.5 V | Large 6E/7 upgrade margin | More upper-band loss; LGA and more demanding RF PCB | **$55.85**, 1772 shown at RFMW | [Manufacturer](https://www.psemi.com/products/rf-switches/broadband-rf-switches/pe42542), [purchase](https://psemi.rfmw.com/products/detail/pe42542-psemi/496644/) | ALTERNATIVE for 7.125 GHz upgrade |
| Mini-Circuits **USB-SP4T-63** | Absorptive SP4T module; 1 MHz–6 GHz; USB control; about 1–1.6 dB loss and 55 dB isolation | Connectorized rapid prototype; software-controlled | Public current price not captured; distributor result reported out of stock; two modules bulky/costly | **PRICE NOT VERIFIED**, availability poor | [Datasheet](https://www.minicircuits.com/WebStore/dashboardPdf?model=USB-SP4T-63) | PROTOTYPING ALTERNATIVE |

## RF filters

| Band / manufacturer / MPN | Passband and evidence | Key RF data | Price / stock | Decision |
|---|---|---|---|---|
| B1 Mini-Circuits **ZX75BP-915-S+** | 902.5–927.5 MHz, [product](https://www.minicircuits.com/WebStore/dashboard.html?model=ZX75BP-915-S%2B), [datasheet](https://www.minicircuits.com/WebStore/dashboardPdf?model=ZX75BP-915-S%2B) | SMA, 50 Ω; exact RL/IL/rejection use datasheet/S-parameters; max power **NOT VERIFIED in extracted web text** | **$112.40; 33 stock** | **SELECTED WITH EDGE-GAP LIMITATION** |
| B1 Southwest Antennas **902–928 MHz in-line filter** | Exact nominal band, [manufacturer](https://southwestantennas.com/products/filter-modules-diplexers-triplexers/bandpass-filter-902-928-mhz-in-line) | Detailed IL/RL/rejection/power and current price **NOT VERIFIED** | **PRICE NOT VERIFIED** | ALTERNATIVE if quote closes edge gaps |
| B2 Mini-Circuits **VBF-2450+** | 2400–2550 MHz, [product](https://www.minicircuits.com/WebStore/dashboard.html?model=VBF-2450%2B), [datasheet](https://www.minicircuits.com/WebStore/dashboardPdf?model=VBF-2450%2B) | SMA 50 Ω; 14 dB typical RL, 30 dB typical stopband rejection, 2 W; roughly 1.7–2.1 dB typical IL | **$47.36; 145 stock** | **SELECTED** |
| B2 Mini-Circuits **ZVBP-2450A-S+** | 2400–2500 MHz exact IoT band, [product](https://www.minicircuits.com/WebStore/dashboard.html?model=ZVBP-2450A-S%2B) | Compact cavity; ≥57/65 dB listed rejection regions; detailed IL/RL/power in datasheet | **$455.23; 20 stock** | ALTERNATIVE when stronger rejection justifies cost |
| B3 Mini-Circuits **BFCN-3600+** | 3300–3900 MHz, [product](https://www.minicircuits.com/WebStore/dashboard.html?model=BFCN-3600%2B), [datasheet](https://www.minicircuits.com/pdfs/BFCN-3600.pdf) | LTCC 50 Ω; 1.2:1 typical VSWR; ≥20 dB catalog rejection regions; connector-board IL TBD | **$5.78 at qty 20; >1000 stock** | **SELECTED on characterized PCB** |
| B3 Mini-Circuits **ZVBP-3500-S+** | 3400–3600 MHz, [datasheet](https://www.minicircuits.com/pdfs/ZVBP-3500-S%2B.pdf) | Cavity; fails full 3.3–3.7 GHz endpoints | Current price **NOT VERIFIED** | REJECTED for incomplete coverage |
| B4 Mini-Circuits **VBFZ-5500-S+** | 4900–6200 MHz, [product](https://www.minicircuits.com/WebStore/dashboard.html?model=VBFZ-5500-S%2B), [datasheet](https://www.minicircuits.com/WebStore/dashboardPdf?model=VBFZ-5500-S%2B) | SMA 50 Ω; IL/RL/rejection/power taken from datasheet/S-parameters during incoming inspection | **$54.13; >100 stock** | **SELECTED** |

No LNA is selected. Indoor exposure hotspots can overload a wideband receiver; filtering and a removable 10 dB pad improve overload margin. An LNA is an optional, separately calibrated weak-signal mode only.

## Discrete antennas

| Manufacturer / MPN | Specifications | Advantages | Limitations | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| Taoglas **TG.66.A113** | Hinged monopole, 600–6000 MHz, SMA male, 50 Ω, 70.3×9.7 mm; manufacturer supplies gain/efficiency/pattern plots, not calibrated antenna factor | One element type spans all baseline bands; compact and replaceable | Requires ground plane; three units do not automatically produce isotropy; coupling, cable routing, pattern distortion and antenna factor TBD | **$19.41 each; 2237 immediate** | [Manufacturer](https://www.taoglas.com/product/apex-tg-66-miniature-5g-4g-terminal/), [datasheet](https://www.taoglas.com/datasheets/TG.66.A113.pdf), [DigiKey](https://www.digikey.com/en/product-highlight/t/taoglas/tg-66-5g-4g-antenna) | **SELECTED for research baseline**, conditional on radiated calibration |
| Taoglas **TG.62.A113** | Original proposal/project text uses variants such as TG.62.A113/TG664133 | Exact identity and authoritative specification were not established; name in image likely conflates `TG.66.A113` | **PRICE NOT VERIFIED** | NOT VERIFIED | REJECTED until exact MPN supplied |
| Band-specific resonant dipoles | Optimized elements per band | Better match/efficiency and possibly easier antenna-factor characterization | Four XYZ triplets imply 12 elements, more switches, mutual coupling and mechanics | **PRICE NOT VERIFIED** | NOT VERIFIED | ARCHITECTURE-B ALTERNATIVE |

## Triaxial/isotropic probes and antennas

| Manufacturer / MPN | Verified behavior | Advantages | Limitations / interface | Price / availability | Evidence | Decision |
|---|---|---|---|---|---|---|
| Aaronia **IsoLOG 3D Mobile 9060 PRO** | 9 kHz–6 GHz; 50 Ω N female RF output; axis switching/chopping; two bypassable +15 dB preamps; USB control; internal battery; up to 6 h | Raw selected RF is analyzer/SDR compatible; commercially integrated axes; baseline-band coverage | Published product page does not establish accredited antenna factor, field-strength accuracy or isotropy uncertainty; internal switching/preamp losses require calibration | **€1,998 manufacturer / $2,697.30 US specialist**, available/orderable | [Manufacturer](https://aaronia.com/en/produkte/antennas/isolog-3d-mobile), [US price](https://www.tequipment.net/Aaronia-pricelist/) | **BEST COMMERCIAL SDR-COMPATIBLE ALTERNATIVE**, omitted to retain budget margin |
| Narda **EF0691 (2402/14B)** | 100 kHz–6 GHz; 0.2–650 V/m; three orthogonal dipoles with detector diodes; individual detected component voltages; correction data in probe EPROM for NBM meter | Broadband isotropic calibrated probe; 70 dB stated dynamic range | Output is detected analog/proprietary probe interface, not 50 Ω raw RF; requires NBM base unit/converter; destroys frequency selectivity | **PRICE NOT VERIFIED**; quote-based | [Manufacturer](https://www.narda-sts.com/en/products/emf-measuring-devices-and-solutions/nbm-probes/), [datasheet](https://www.narda-sts.com/index.php?dl=1&eID=dumpFile&f=1304&t=f&token=bb5dc97e7304ccb419270f8a799d2cd8f87e4144) | **NOT DIRECTLY SDR-COMPATIBLE**; reference-instrument alternative |
| Wavecontrol **WPF8** | 100 kHz–8 GHz; isotropic RMS diode; 0.2–130 V/m CW; 52 dB; ±1.5 dB frequency response to 6 GHz; ±1 dB isotropy at 2 GHz; ISO 17025 calibration available | Strong broadband screening/traceability; covers optional 6 GHz | Detected/proprietary interface for SMP2/MonitEM/MapEM; no raw frequency-selective RF output | Quote; **PRICE NOT VERIFIED** | [Manufacturer](https://www.wavecontrol.com/emf-products/electric-field-probes/), [datasheet](https://www.wavecontrol.com/rfsafety/images/data-sheets/en/WPF8_Datasheet_EN.pdf) | **NOT DIRECTLY SDR-COMPATIBLE**; reference comparison alternative |

The commercial probe class and the frequency-selective SDR class should be viewed as complementary. A broadband calibrated probe is the preferred comparison instrument; it cannot replace the SDR’s band-resolved chain unless it supplies raw RF or synchronized calibrated per-axis data.
