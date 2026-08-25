Wireless IoT protocols for indoor environments in Colombia span Sub-1 GHz (902–928 MHz), 2.4 GHz (ISM), 3.3–3.7 GHz, and 5/6 GHz bands, balancing latency, obstacle penetration, power consumption, and data throughput.

Non-measurable or non-applicable entries for Colombia (such as **Amazon Sidewalk** due to regional geoblocking, **DECT ULE** due to frequency allocation conflicts, **NFC** due to non-propagating near-field coupling, and **Matter** due to being an application-layer standard rather than an RF protocol) have been removed.

| Protocol | Category | Primary Frequency Band(s) in Colombia | Primary Indoor Use Case (Colombia Context) |
| --- | --- | --- | --- |
| **WirelessHART** | Industrial | 2.4 GHz (ISM) | Process automation and field instrument telemetry in oil & gas, energy, and manufacturing plants. |
| **ISA100.11a** | Industrial | 2.4 GHz (ISM) | Heavy-industrial sensor monitoring and safety instrumented systems. |
| **Industrial Wi-Fi (5/6/6E/7)** | Industrial / Commercial | 2.4 GHz, 5 GHz, 6 GHz (5.925–7.125 GHz) | Autonomous Mobile Robots (AMRs/AGVs), high-throughput video inspection, and plant telemetry. |
| **Private 5G Indoor** | Industrial | Sub-6 GHz (3.3–3.7 GHz) | Ultra-low-latency mission-critical control, mobile robotics, and real-time digital twins under ANE NPN frameworks. |
| **LoRaWAN (Indoor)** | Industrial / Commercial | Sub-1 GHz (902–928 MHz - US915 / AU915) | Deep-building penetration, energy/water sub-metering, and smart building monitoring. |
| **Zigbee** | Commercial / Residential | 2.4 GHz (ISM), 915 MHz | Residential home automation, commercial smart lighting, and HVAC control. |
| **Z-Wave** | Commercial / Residential | Sub-1 GHz (908.42 / 916 MHz) | Residential security, smart locks, and sensors avoiding 2.4 GHz congestion. |
| **Thread** | Commercial / Industrial | 2.4 GHz (ISM) | Low-power IP mesh networking for smart home and building automation systems. |
| **Bluetooth LE / Mesh** | Commercial / Industrial | 2.4 GHz (ISM) | Indoor asset tracking, location beacons, smart lighting, and consumer wearables. |

---

In Colombia, the National Spectrum Agency (ANE) regulates license-exempt spectrum (ISM and UNII bands) under Resolution 105 of 2020 and Resolution 737 of 2022, while allocating dedicated Sub-6 GHz segments for private industrial deployments.

| Frequency Band | Protocol / Service | ANE Regulatory Status (Colombia) | Channel Bandwidth |
| --- | --- | --- | --- |
| **902 – 928 MHz**<br>(Sub-1 GHz ISM) | LoRaWAN (US915 / AU915) | Unlicensed / License-Exempt (Res. 105/2020) | 125 kHz (Uplink) / 500 kHz (Downlink / High-speed) |
| **902 – 928 MHz**<br>(Sub-1 GHz ISM) | Z-Wave (908.42 / 916 MHz) | Unlicensed / License-Exempt (Res. 105/2020) | 100 kHz to 400 kHz |
| **902 – 928 MHz**<br>(Sub-1 GHz ISM) | Zigbee Sub-GHz (915 MHz) | Unlicensed / License-Exempt (Res. 105/2020) | 600 kHz to 2 MHz |
| **2.400 – 2.4835 GHz**<br>(2.4 GHz ISM) | Wi-Fi (802.11 b/g/n/ax/be) | Unlicensed / License-Exempt (Res. 105/2020) | 20 MHz / 40 MHz |
| **2.400 – 2.4835 GHz**<br>(2.4 GHz ISM) | Bluetooth LE / Mesh | Unlicensed / License-Exempt (Res. 105/2020) | 2 MHz (40 channels @ 2 MHz spacing) |
| **2.400 – 2.4835 GHz**<br>(2.4 GHz ISM) | Zigbee / Thread (2.4 GHz) | Unlicensed / License-Exempt (Res. 105/2020) | 2 MHz channel bandwidth (5 MHz spacing) |
| **2.400 – 2.4835 GHz**<br>(2.4 GHz ISM) | WirelessHART / ISA100.11a | Unlicensed / License-Exempt (Res. 105/2020) | 2 MHz (based on IEEE 802.15.4 PHY) |
| **3.300 – 3.700 GHz**<br>(Sub-6 GHz) | Private 5G Indoor (NPN Networks) | Reserved / Licensed for Private NPNs | 10 MHz to 100 MHz (per NR carrier) |
| **5.150 – 5.850 GHz**<br>(5 GHz UNII) | Industrial Wi-Fi 5 / Wi-Fi 6 | Unlicensed Indoor Use (Res. 105/2020) | 20 MHz, 40 MHz, 80 MHz, 160 MHz |
| **5.925 – 7.125 GHz**<br>(6 GHz Unlicensed) | Industrial Wi-Fi 6E / Wi-Fi 7 | Unlicensed Indoor Use (Res. 737/2022) | 20 MHz, 40 MHz, 80 MHz, 160 MHz, 320 MHz |

---

**Technical and Regulatory Notes for Colombia**

* **6 GHz Band (5.925 – 7.125 GHz):** Resolution 737 of 2022 opened the entire 1,200 MHz contiguous block for license-exempt indoor applications, enabling industrial deployments to utilize ultra-wide 320 MHz channels under Wi-Fi 7.
* **Sub-1 GHz Band (902 – 928 MHz):** Operates under unlicensed ISM conditions. LoRaWAN deployments follow the US915 or AU915 channel plans, using 125 kHz channels for power-efficient sensor uplinks and 500 kHz channels for downlinks or higher throughput.
* **Sub-6 GHz Private Networks (3.300 – 3.700 GHz):** The ANE framework supports Non-Public Networks (NPN) tailored for industrial plants, logistics hubs, and mining sites, offering scalable carrier bandwidths from 10 MHz to 100 MHz over 5G NR (n77/n78 bands).
