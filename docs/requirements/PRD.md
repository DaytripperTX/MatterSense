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
- Permit direct local sensor access and selected configuration over BLE when no Thread Border Router or Matter controller is available
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
- Secure BLE Local Mode for standalone sensor access and selected local configuration
- Matter-over-Thread operation
- Core environmental sensing (T/H, eCO₂/VOC, lux)
- Barometric pressure capability
- Secure OTA staging and local sensor-data buffering in external flash
- Ultra-low-power, sleep-centric design
- Thread Border Router and Matter controller/fabric required for Matter-over-Thread, but not for BLE Local Mode

---

### 5.3 Revision B (Planned)
- Dual power modes: battery-powered or USB-powered
- USB 2.0 device data for service logs, recovery, and wired firmware update support
- Matter-over-Wi-Fi operation without requiring a Thread Border Router
- Matter-over-Thread build option with Wi-Fi disabled or the footprint-compatible Thread-only host-module population
- Secure BLE Local Mode independent of Thread and Wi-Fi availability
- NFC-assisted commissioning
- Power-mode–aware radio behavior:
  - Supported associated Wi-Fi power-save behavior when battery-powered
  - Always-reachable operation when externally powered
- Same baseline sensor population as Rev A, with support for future higher-power variants
- Improved onboarding and user experience

### 5.4 Product Requirement Identifiers

The following identifiers are the normative product-level requirements used by the
Requirements Traceability Matrix. The feature summaries above provide context but
do not replace these requirements.

| ID | Product requirement |
|---|---|
| PRD-1 | The product shall monitor indoor temperature and relative humidity. |
| PRD-2 | The product shall monitor VOC and equivalent CO2 trends. |
| PRD-3 | The product shall monitor ambient light level in lux. |
| PRD-4 | The product shall provide barometric-pressure capability from the baseline environmental sensor. |
| PRD-5 | A future externally powered variant may monitor non-recording ambient sound level; neither baseline revision requires a microphone. |
| PRD-6 | Sensor sampling and reporting intervals shall be configurable within the supported power and protocol limits. |
| PRD-7 | Baseline environmental data shall be reportable through Matter-compatible device behavior and clusters. |
| PRD-8 | Optional capabilities shall be exposed only when supported by the assembled hardware and firmware configuration. |
| PRD-9 | Both revisions shall support BLE-based Matter commissioning. |
| PRD-10 | Rev B shall support Matter-over-Wi-Fi without requiring a Thread Border Router. |
| PRD-11 | Both revisions shall support battery-powered operation. |
| PRD-12 | Battery-powered operation shall provide multi-month runtime under the defined typical-use profile. |
| PRD-13 | Rev B shall accept USB-C power, charge its battery, and provide USB 2.0 device data for service, recovery, and wired update workflows. |
| PRD-14 | Radio behavior and responsiveness shall adapt to the available power source and selected product configuration. |
| PRD-15 | The product shall use a sleep-centric, low-average-power operating model. |
| PRD-16 | The product shall recover safely from reset, brownout, and interrupted power events. |
| PRD-17 | The product shall support authenticated firmware updates with a non-bricking recovery path. |
| PRD-18 | Wireless links and provisioned device credentials shall be protected against unauthorized access. |
| PRD-19 | Firmware shall remain modular and revision-aware so shared sensing and application behavior can be reused across hardware revisions. |
| PRD-20 | Sound-level functionality, if fitted in a future variant, shall not record or store audio. |
| PRD-21 | Rev A shall support Matter-over-Thread; Rev B shall retain a Matter-over-Thread build option. |
| PRD-22 | The product shall support bounded, wear-aware local buffering of selected sensor history. |
| PRD-23 | Both revisions shall provide secure BLE Local Mode for sensor access and selected local configuration without Thread, Wi-Fi, or Matter infrastructure. |

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
- Rev A devices require a Thread Border Router and Matter controller/fabric for Matter-over-Thread, but remain usable through BLE Local Mode without either
- Rev B devices may use Matter-over-Wi-Fi without a Thread Border Router; BLE Local Mode remains available without a Matter controller/fabric
- BLE Local Mode is a product-specific GATT interface, not a Matter operational transport
- Power availability directly influences radio availability and responsiveness

---

## 8. Success Metrics

- Successful commissioning with at least one Matter ecosystem
- Stable reporting of environmental sensor data over extended runtime
- Reliable local readout and selected configuration through BLE without Thread or Wi-Fi infrastructure
- Successful signed firmware update with recovery from an interrupted transfer
- Reliable circular buffering and retrieval of selected sensor history
- Multi-month battery life under typical indoor conditions
- Seamless transition between power modes in Rev B
- Shared sensing functions, application behavior, and firmware abstractions reused across revisions without redesign of those common functions

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
| BLE Local Mode reducing battery life | Low-duty or user-initiated advertising policy, authenticated connections, and measured current budget |
| Battery life degradation | Feature gating when on battery |
| Flash wear or interrupted writes | Buffered writes, circular storage, integrity metadata, and recovery testing |
| User privacy concerns | No audio recording, no cloud dependency |

---

## 11. Notes

This document intentionally avoids binding requirements to specific ICs or vendors.
Component selection and implementation details are defined in the HRS, SRS,
hardware block-selection record, and downstream design documentation.
