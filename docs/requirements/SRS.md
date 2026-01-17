# Software Requirements Specification (SRS)
## MatterSense System

> This document defines the functional and non-functional requirements for the
> MatterSense hardware and firmware system. It is a living document and may evolve
> as the implementation matures.

---

## 1. Introduction

This document defines the functional and non-functional requirements for the MatterSense
hardware and firmware system. It translates product requirements into clear, testable engineering specifications.

---

## 2. System Overview

MatterSense is a battery-powered embedded system built around a Nordic BLE SoC with
optional Wi-Fi expansion, integrated environmental sensors, and a Matter-compatible
protocol stack.

The system supports multiple power modes that directly influence wireless behavior
and system availability.

---

## 3. System Architecture

### 3.1 Hardware
- SoC: Nordic nRF52840
- Sensors:
  - Temperature & humidity
  - eCO₂ / VOC
  - Ambient light (lux)
  - Optional barometric pressure
  - Optional ambient sound level (dB)
- Power:
  - Battery-powered operation
  - USB-powered operation (Rev B)
- Debug: SWD interface
- Expansion: NFC pins routed for Rev B compatibility

---

### 3.2 Wireless Connectivity
- Rev A:
  - Bluetooth Low Energy only
- Rev B:
  - BLE + Wi-Fi
  - Power-mode–dependent radio operation

---

## 4. Functional Requirements

### 4.1 Sensor Acquisition
- FR-1: The system shall periodically sample temperature data.
- FR-2: The system shall periodically sample humidity data.
- FR-3: The system shall periodically sample eCO₂ and VOC data.
- FR-4: The system shall periodically sample ambient light level data.
- FR-5: The system shall optionally sample barometric pressure data.
- FR-6: The system shall optionally sample ambient sound level (dB) data.
- FR-7: Sensor sampling intervals shall be configurable at build time or firmware configuration time.

---

### 4.2 Data Reporting
- FR-8: Sensor data shall be exposed via Matter-compatible clusters.
- FR-9: The system shall support at least one Matter endpoint.
- FR-10: Optional sensors shall be conditionally exposed when populated.

---

### 4.3 Wireless Operation
- FR-11: The system shall support BLE-based commissioning.
- FR-12: The system shall maintain reliable wireless connectivity during commissioning.
- FR-13: The system shall support Wi-Fi connectivity in Rev B hardware.

---

### 4.4 Power-Mode–Aware Operation (Rev B)
- FR-14: The system shall detect whether it is battery-powered or externally powered.
- FR-15: When battery-powered, the system shall aggressively duty-cycle wireless radios.
- FR-16: When battery-powered, the system shall not be continuously reachable over Wi-Fi.
- FR-17: When externally powered, the system shall maintain continuous network availability.
- FR-18: The system shall transition cleanly between power modes without reboot or data loss.

---

### 4.5 Power Management
- FR-19: The system shall enter a low-power sleep state between sensor measurements.
- FR-20: The system shall minimize average current consumption when battery-powered.
- FR-21: The system shall recover gracefully from brown-out or power interruption events.

---

### 4.6 Firmware Updates
- FR-22: The system shall support secure firmware updates over the air (planned).
- FR-23: Firmware updates shall not compromise device security or integrity.

---

## 5. Non-Functional Requirements

### 5.1 Power
- NFR-1: Battery-powered operation shall support multi-month battery life under typical use.
- NFR-2: Sleep current shall be minimized consistent with hardware capability.
- NFR-3: Externally powered operation may prioritize responsiveness over power savings.

---

### 5.2 Reliability
- NFR-4: The system shall resume normal operation after reset or power loss.
- NFR-5: The system shall avoid unrecoverable fault states.

---

### 5.3 Security
- NFR-6: Wireless communication shall be encrypted.
- NFR-7: Unauthorized firmware installation shall be prevented.

---

### 5.4 Maintainability
- NFR-8: Firmware shall be modular and revision-aware.
- NFR-9: Hardware revision changes shall minimize firmware rework.

---

## 6. Interface Requirements

### 6.1 Debug Interface
- SWD shall be accessible for development and test.

### 6.2 External Interfaces
- NFC interface pins shall be routed and electrically supported.
- Test points shall be provided for power and ground.

---

## 7. Environmental Requirements

- Intended for indoor residential environments
- Non-condensing humidity conditions

---

## 8. Verification & Validation

| Requirement Category | Verification Method |
|---------------------|---------------------|
| Sensor acquisition | Functional testing |
| Power consumption | Current profiling |
| Wireless behavior | Connectivity testing |
| Power-mode switching | Mode transition testing |

---

## 9. Future Extensions

- Additional Matter clusters
- Expanded sensor SKUs
- Wi-Fi-only or externally powered variants
