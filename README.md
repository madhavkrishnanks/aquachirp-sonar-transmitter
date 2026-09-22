# 🌊 AquaChirp — Software-Defined Sonar Transmitter & Acoustic Edge Intelligence Platform

[![Vite](https://img.shields.io/badge/Vite-6.x-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=black)](https://reactjs.org/)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![STM32](https://img.shields.io/badge/Hardware-STM32F103RB-03234B?logo=stmicroelectronics&logoColor=white)](https://www.st.com/)
[![WebSerial](https://img.shields.io/badge/Protocol-WebSerial_API-0284C7?logo=googlechrome&logoColor=white)](https://developer.mozilla.org/en-US/docs/Web/API/Web_Serial_API)
[![License: MIT](https://img.shields.io/badge/License-MIT-emerald.svg)](LICENSE)

# KESTREL — Adaptive Software-Defined Sonar Transmitter Payload for AUVs

### SIH26058 | Low-Power, Real-Time Adaptive Sonar Transmitter

### Hardware & Prototype Snapshot

**Measured on Rigol DS1054Z**

| Barker 13 | CW Signal | LFM Chirp |
| :---: | :---: | :---: |
| <img width="260" alt="barker 13" src="https://github.com/user-attachments/assets/f8ebcaf2-d14b-4bde-b417-52c8f93ccc08" /> | <img width="260" alt="cw" src="https://github.com/user-attachments/assets/e0507b65-0fdb-4f71-9c58-35297c904baf" /> | <img width="260" alt="lfm chirp" src="https://github.com/user-attachments/assets/793cc427-37d6-4ed2-913a-4ecc7a9709ea" /> |

| Embedded Core | DAC | Physical Validation | Mechanical Integration |
|---|---|---|---|
| STM32F103RBT6 | MCP4921 12-bit | Rigol DS1054Z | AUV 3D CAD |


A compact STM32-based software-defined sonar transmitter payload that adapts its transmitted waveform parameters using environmental inputs such as depth, temperature, turbidity, salinity, and resolution–penetration preference.

> **Smart India Hackathon 2026 — Problem Statement SIH26058**  
> **Team:** KESTREL  
> **Team ID:** 127602  
> **Institution:** SRM Institute of Science and Technology, Ramapuram

## Problem Statement

Conventional sonar transmitters are often designed around fixed transmission parameters, making them less flexible when underwater environmental conditions change.

For an AUV operating in varying conditions, parameters such as depth, temperature, turbidity, salinity, and the required balance between resolution and penetration can influence the choice of transmission strategy.

The SIH26058 problem calls for a **software-defined, low-power and real-time adaptive sonar transmitter payload** capable of generating configurable waveforms and adapting its transmission parameters according to environmental conditions.

## Our Solution

KESTREL proposes a **software-defined sonar transmitter payload** built around an STM32 microcontroller and a configurable analog signal chain.

The system continuously processes environmental inputs and selects an appropriate transmission strategy. The selected waveform is generated digitally, streamed through the DAC using hardware-timed data transfer, reconstructed through the analog signal chain, and validated using an oscilloscope.

## Core Approach

**Sense → Adapt → Synthesize → Convert → Filter → Validate**

1. **Sense** — Acquire environmental parameters such as depth, temperature, turbidity and salinity.
2. **Adapt** — Determine the transmission strategy based on the current environmental conditions and resolution–penetration preference.
3. **Synthesize** — Generate the required waveform digitally using firmware.
4. **Convert** — Convert the digital samples into an analog signal using the MCP4921 DAC.
5. **Filter & Buffer** — Reconstruct and condition the waveform through the analog filter, CD4053B switching stage and MCP6004 buffer.
6. **Validate** — Observe the electrical output in the time domain and frequency domain using a digital oscilloscope.

The design is intended as a **modular transmitter payload**, allowing waveform-generation and environmental-adaptation logic to be modified through firmware without redesigning the complete hardware signal chain.

## How KESTREL Addresses the PS

| PS Requirement | KESTREL Implementation |
|---|---|
| Software-defined transmitter | STM32-based firmware-controlled waveform generation |
| Real-time adaptation | Environmental inputs drive transmission-strategy selection |
| Multiple waveform types | CW, LFM chirp and phase-coded/Barker-based modes |
| Low-power operation | Timer + DMA used for waveform sample streaming |
| Digital waveform synthesis | Firmware-generated waveform samples |
| Analog transmission chain | MCP4921 DAC → filtering → CD4053B → MCP6004 |
| Real-time environmental inputs | Temperature, turbidity, salinity/TDS, depth and resolution–penetration preference |
| Waveform validation | Rigol DS1054Z time-domain and FFT measurements |
| AUV payload integration | Compact 3D-designed payload enclosure |

## System Architecture

The KESTREL transmitter is organized as a modular embedded signal-generation pipeline.

```text
                 ENVIRONMENTAL INPUTS
        ┌─────────────────────────────────────┐
        │ Depth                               │
        │ Temperature                         │
        │ Turbidity                           │
        │ Salinity / TDS                     │
        │ Resolution–Penetration Preference  │
        └──────────────────┬──────────────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │   STM32F103RBT6     │
                │                     │
                │ Sensor Acquisition  │
                │ Adaptive Logic      │
                │ Waveform Generation │
                └──────────┬──────────┘
                           │
                    SPI + Timer + DMA
                           │
                           ▼
                ┌─────────────────────┐
                │   MCP4921 DAC       │
                │      12-bit         │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │ Reconstruction /    │
                │ RC Filtering        │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │    CD4053B          │
                │ Filter Selection    │
                └──────────┬──────────┘
                           │
                           ▼
                ┌─────────────────────┐
                │    MCP6004          │
                │ Buffer / Conditioning│
                └──────────┬──────────┘
                           │
                           ▼
                    ANALOG OUTPUT
                           │
                           ▼
                ┌─────────────────────┐
                │   Digital Storage   │
                │   Oscilloscope      │
                │                     │
                │ Time Domain + FFT   │
                └─────────────────────┘

  ```           
## Signal flow

```text
Environmental Inputs
        ↓
STM32 Data Acquisition
        ↓
Adaptive Transmission Logic
        ↓
Waveform Synthesis
        ↓
Timer + DMA Sample Streaming
        ↓
MCP4921 12-bit DAC
        ↓
Analog Reconstruction Filter
        ↓
CD4053B Filter Selection
        ↓
MCP6004 Buffer
        ↓
Analog Output
        ↓
Oscilloscope Validation
  ``` 
---

## Experimental Validation

### CW
["https://github.com/user-attachments/assets/e0507b65-0fdb-4f71-9c58-35297c904baf"]

### LFM Chirp
["https://github.com/user-attachments/assets/793cc427-37d6-4ed2-913a-4ecc7a9709ea"]

### Barker 13 Test
["https://github.com/user-attachments/assets/f8ebcaf2-d14b-4bde-b417-52c8f93ccc08"]

### Power Measurement

The transmitter signal chain was measured at 3.3 V using a digital multimeter
connected in series with the supply.

| Waveform Mode | Measured Power | Equivalent Current |
|---|---:|---:|
| CW | 8.25 mW | 2.50 mA |
| LFM | 8.05 mW | 2.44 mA |
| Barker-13 | 7.95 mW | 2.41 mA |

[dmm high.png]

Across the tested waveform modes, the measured signal-chain power remained
within **7.95–8.25 mW**, corresponding to approximately **2.41–2.50 mA at
3.3 V**.

This indicates that changing the waveform mode does not introduce a large
change in the measured signal-chain power.

[power varada.png]

> **Measurement scope:** These values represent the measured 3.3 V
> transmitter signal-chain section and do not represent the total power
> consumption of the complete development-board system.


## AUV Mechanical Integration

A 3D CAD enclosure was developed to explore the mechanical integration of the
transmitter electronics into an AUV payload.

![AUV Payload CAD]([Blue Underwater Vehicle Callout Diagram.png])

### Design Highlights

- Compact cylindrical AUV-compatible form factor
- Internal electronics cavity
- Removable service panel
- Rear cable pass-through
- Sensor mounting provisions
- PCB / electronics mounting provisions

The current CAD represents a **prototype mechanical integration concept** and
is not claimed as a pressure-rated underwater housing.


## 🚀 Getting Started

### Prerequisites
- [Node.js](https://nodejs.org/) (v18 or higher)
- Google Chrome, Microsoft Edge, or any Chromium browser supporting the **WebSerial API**

### 1. Clone the Repository
```bash
git clone https://github.com/D-Tharun/aquachirp-sonar-transmitter.git
cd aquachirp-sonar-transmitter
```

### 2. Install Dependencies
```bash
npm install
```

### 3. Start Local Development Server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

### 4. Build for Production Deployment
```bash
npm run build
```

---

## 🚢 Continuous Deployment & Hosting

This repository is configured for zero-configuration 1-click deployment on **Vercel** or **Netlify**:

1. Push this repository to GitHub.
2. Link the repository to [Vercel](https://vercel.com) or [Netlify](https://netlify.com).
3. The platform will automatically build and deploy the application on every `git push` or merged Pull Request.
4. Access the live HTTPS dashboard and click **CONNECT USB** to link your physical STM32 hardware!

---

## 📄 License
This project is open-source and available under the [MIT License](LICENSE).
