Wireless IoT protocols for indoor environments in Colombia span Sub-1 GHz (902–928 MHz), 2.4 GHz (ISM), 3.3–3.7 GHz, and 5/6 GHz bands, balancing latency, obstacle penetration, power consumption, and data throughput.

Non-measurable or non-applicable entries for Colombia (such as **Amazon Sidewalk** due to regional geoblocking, **DECT ULE** due to frequency allocation conflicts, **NFC** due to non-propagating near-field coupling, and **Matter** due to being an application-layer standard rather than an RF protocol) have been removed.

| Protocol | Category | Primary Frequency Band(s) in Colombia | Primary Indoor Use Case (Colombia Context) |
| --- | --- | --- | --- |
| **WirelessHART** | Industrial | 2.4 GHz (ISM) | Process automation and field instrument telemetry in oil & gas, energy, and manufacturing plants.

 |
| **ISA100.11a** | Industrial | 2.4 GHz (ISM) | Heavy-industrial sensor monitoring and safety instrumented systems.

 |
| **Industrial Wi-Fi (5/6/6E/7)** | Industrial / Commercial | 2.4 GHz, 5 GHz, 6 GHz (5.925–7.125 GHz) | Autonomous Mobile Robots (AMRs/AGVs), high-throughput video inspection, and plant telemetry.

 |
| **Private 5G Indoor** | Industrial | Sub-6 GHz (3.3–3.7 GHz) | Ultra-low-latency mission-critical control, mobile robotics, and real-time digital twins under ANE NPN frameworks.

 |
| **LoRaWAN (Indoor)** | Industrial / Commercial | Sub-1 GHz (902–928 MHz - US915 / AU915) | Deep-building penetration, energy/water sub-metering, and smart building monitoring.

 |
| **Zigbee** | Commercial / Residential | 2.4 GHz (ISM), 915 MHz | Residential home automation, commercial smart lighting, and HVAC control.

 |
| **Z-Wave** | Commercial / Residential | Sub-1 GHz (908.42 / 916 MHz) | Residential security, smart locks, and sensors avoiding 2.4 GHz congestion.

 |
| **Thread** | Commercial / Industrial | 2.4 GHz (ISM) | Low-power IP mesh networking for smart home and building automation systems.

 |
| **Bluetooth LE / Mesh** | Commercial / Industrial | 2.4 GHz (ISM) | Indoor asset tracking, location beacons, smart lighting, and consumer wearables.

 |

---

### RF Frequency Bands Diagram (Colombia Indoor IoT)

```
==============================================================================================
                          INDOOR IOT RF SPECTRUM MAP (COLOMBIA)
==============================================================================================

  Frequency Band                                Protocols Operating
----------------------------------------------------------------------------------------------

 [ 902 – 928 MHz ]     |=================================================================|
 Sub-1 GHz (ISM)       |-- LoRaWAN (US915 / AU915)
                       |-- Z-Wave (908.42 MHz / 916 MHz)
                       |-- Zigbee Sub-GHz (915 MHz)

 [ 2.400 – 2.4835 GHz ]|=================================================================|
 2.4 GHz ISM           |-- Wi-Fi (802.11 b/g/n/ax/be)
                       |-- Bluetooth LE / Mesh
                       |-- Zigbee (2.4 GHz) / Thread
                       |-- WirelessHART / ISA100.11a

 [ 3.300 – 3.700 GHz ] |==================================|
 Sub-6 GHz             |-- Private 5G Indoor (ANE NPN License)

 [ 5.150 – 5.850 GHz ] |=================================================|
 5 GHz UNII / Wi-Fi    |-- Industrial Wi-Fi 5 / Wi-Fi 6

 [ 5.925 – 7.125 GHz ] |==================================================================|
 6 GHz Unlicensed      |-- Industrial Wi-Fi 6E / Wi-Fi 7 (Unlicensed ANE Allocation)

==============================================================================================

```