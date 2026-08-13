# Product Requirements Document (PRD)
## MatterSense Environmental Sensor Node

> This document defines the product-level requirements for the MatterSense project.
> It is a living document and may evolve as the system architecture and hardware
> design mature.

---

## 1. Purpose

This document defines the product requirements for **MatterSense**, a battery-powered environmental sensor node designed for integration with Matter-compatible
smart home ecosystems. The PRD focuses on *what* the product does and *why*, without
binding the design to specific component implementations.

---

## 2. Product Overview

MatterSense is a low-power, multi-sensor environmental monitoring device intended
for integration with Matter-compatible smart home ecosystems. The product is designed and documented using
professional product development practices and with intent for potential eventual mass
manufacture.

The system architecture supports multiple hardware revisions, enabling feature
expansion and power-scaling without fundamental redesign.

---

## 3. Target Audience

- Smart home users seeking interoperable environmental monitoring
- Engineers evaluating Matter-based device architectures
- Portfolio reviewers assessing embedded systems and PCB design capability
- Manufacturers evaluating reference system concepts

---

## 4. Use Cases

- Monitor indoor air quality trends using eCO₂ and VOC metrics
- Monitor ambient light levels (lux) for automation and analytics
- Provide environmental context via barometric pressure
- Optionally monitor ambient sound levels in a future externally powered variant (non-audio)
- Enable smart home automations based on environmental conditions
- Demonstrate Matter device commissioning and interoperability
- Buffer selected sensor history locally when configured

---

## 5. Key Features

### 5.1 Core Sensor Capabilities
- Temperature and humidity sensing
- Equivalent CO₂ (eCO₂) and VOC sensing
- Ambient light level (lux) sensing
- Barometric pressure capability from the baseline environmental sensor
- Optional ambient sound level (dB) sensing in a future externally powered variant
- Local nonvolatile buffering of selected sensor records

> Sound sensing is limited to sound pressure level measurement only and does not
> include audio recording, storage, or voice processing.

---

### 5.2 Revision A (Initial Release)
- Battery-powered operation
- BLE-based commissioning
- Matter-over-Thread operation
- Core environmental sensing (T/H, eCO₂/VOC, lux)
- Barometric pressure capability
- Secure OTA staging and local sensor-data buffering in external flash
- Ultra-low-power, sleep-centric design
- Thread Border Router and Matter controller/fabric required

---

### 5.3 Revision B (Planned)
- Dual power modes: battery-powered or USB-powered
- Matter-over-Wi-Fi operation without requiring a Thread Border Router
- Matter-over-Thread build/population option when Wi-Fi is unpopulated
- NFC-assisted commissioning
- Power-mode–aware radio behavior:
  - Supported associated Wi-Fi power-save behavior when battery-powered
  - Always-reachable operation when externally powered
- Same baseline sensor population as Rev A, with support for future higher-power variants
- Improved onboarding and user experience

---

## 6. Product Constraints

- Components must be available from U.S.-based distributors
- BOM cost suitable for consumer-grade devices
- Compact PCB footprint suitable for small enclosures
- Designed for manufacturability and assembly
- Battery life measured in months under typical usage
- Compliance with Matter ecosystem expectations

---

## 7. Assumptions

- eCO₂ and VOC values are trend-based and not laboratory-grade
- Sound level sensing is relative and intended for environmental awareness only
- Rev A devices rely on a Thread Border Router and a Matter controller/fabric
- Rev B devices may use Wi-Fi transport without a Thread Border Router but still require a Matter controller/fabric
- Power availability directly influences radio availability and responsiveness

---

## 8. Success Metrics

- Successful commissioning with at least one Matter ecosystem
- Stable reporting of environmental sensor data over extended runtime
- Successful signed firmware update with recovery from an interrupted transfer
- Reliable circular buffering and retrieval of selected sensor history
- Multi-month battery life under typical indoor conditions
- Seamless transition between power modes in Rev B
- No architectural redesign required between revisions

---

## 9. Out of Scope

- Audio capture or speech processing
- Cloud backend services
- Mobile application development
- Regulatory certification beyond design consideration

---

## 10. Risks & Mitigations

| Risk | Mitigation |
|----|----|
| High power draw from air-quality sensors | Aggressive duty cycling and sampling control |
| Wi-Fi power consumption in Rev B | Power-mode–dependent radio behavior |
| Battery life degradation | Feature gating when on battery |
| Flash wear or interrupted writes | Buffered writes, circular storage, integrity metadata, and recovery testing |
| User privacy concerns | No audio recording, no cloud dependency |

---

## 11. Notes

This document intentionally avoids binding requirements to specific ICs or vendors.
Component selection and implementation details are defined in the HRS, SRS,
hardware block-selection record, and downstream design documentation.
