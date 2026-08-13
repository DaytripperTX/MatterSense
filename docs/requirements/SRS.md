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

MatterSense is a battery-powered embedded system built around a Nordic multiprotocol
SoC. Rev A operates as a Matter-over-Thread device commissioned through Bluetooth
Low Energy; Rev B adds an optional Wi-Fi companion for Matter-over-Wi-Fi operation.
Both revisions include environmental sensors and external nonvolatile storage.

The system supports multiple power modes that directly influence wireless behavior
and system availability.

---

## 3. System Architecture

### 3.1 Hardware
- Rev A SoC: Nordic nRF52840 in the Ezurio BL654 module
- Rev B host: nRF5340-class MCU/module selection required to meet the current Nordic Matter-over-Wi-Fi platform requirement with nRF7002
- Sensors:
  - Temperature & humidity
  - eCO₂ / VOC
  - Ambient light (lux)
  - Barometric pressure from the baseline BME688
  - Optional ambient sound level (dB) on a future externally powered variant
- Power:
  - Battery-powered operation
  - USB-powered operation (Rev B)
- Nonvolatile storage:
  - 64-Mbit external QSPI NOR for OTA staging and selected sensor history
- Debug: SWD interface
- Expansion: NFC pins routed for Rev B compatibility

---

### 3.2 Wireless Connectivity
- Rev A:
  - Bluetooth Low Energy commissioning
  - Matter-over-Thread operational transport
- Rev B:
  - Bluetooth Low Energy commissioning
  - Matter-over-Thread and Matter-over-Wi-Fi capability
  - One operational IP transport selected per product/firmware configuration; runtime Thread/Wi-Fi transport switching is not required
  - Power-mode–dependent radio operation

---

## 4. Functional Requirements

### 4.1 Sensor Acquisition
- FR-1: The system shall periodically sample temperature data.
- FR-2: The system shall periodically sample humidity data.
- FR-3: The system shall periodically sample eCO₂ and VOC data.
- FR-4: The system shall periodically sample ambient light level data.
- FR-5: The system shall be capable of periodically sampling barometric pressure data from the baseline BME688; product reporting may be configurable.
- FR-6: The system shall optionally sample ambient sound level (dB) data.
- FR-7: Sensor sampling intervals shall be configurable at build time or firmware configuration time.

---

### 4.2 Data Reporting
- FR-8: Sensor data shall be exposed via Matter-compatible clusters.
- FR-9: The system shall support at least one Matter endpoint.
- FR-10: Optional sensors shall be conditionally exposed when populated.
- FR-25: Firmware shall optionally store selected, timestamped or sequence-numbered sensor records in external flash.
- FR-26: Sensor-history storage shall use a bounded circular or equivalent wear-aware scheme and shall recover cleanly from interrupted writes.

---

### 4.3 Wireless Operation
- FR-11: The system shall support BLE-based commissioning.
- FR-12: The system shall maintain reliable wireless connectivity during commissioning.
- FR-13: Rev B shall support Matter-over-Wi-Fi through the WM02C companion.
- FR-24: Both revisions shall support Matter-over-Thread as a Sleepy End Device or Matter Intermittently Connected Device appropriate to the final application behavior; it is Rev A's normal transport and a separate Rev B firmware/build option when Wi-Fi is unpopulated.

---

### 4.4 Power-Mode–Aware Operation (Rev B)
- FR-14: The system shall detect whether it is battery-powered or externally powered.
- FR-15: When battery-powered, the system shall aggressively duty-cycle wireless radios.
- FR-16: When battery-powered in a Matter-over-Wi-Fi configuration, the system shall use a supported associated power-save mode and meet the selected Matter reachability requirements.
- FR-17: When externally powered, the system shall maintain continuous network availability.
- FR-18: The system shall transition cleanly between power modes without reboot or data loss.

---

### 4.5 Power Management
- FR-19: The system shall enter a low-power sleep state between sensor measurements.
- FR-20: The system shall minimize average current consumption when battery-powered.
- FR-21: The system shall recover gracefully from brown-out or power interruption events.

---

### 4.6 Firmware Updates
- FR-22: The system shall support secure firmware updates over the air.
- FR-23: Firmware updates shall not compromise device security or integrity.
- FR-27: OTA images shall be staged in the external QSPI NOR secondary slot and authenticated before activation.
- FR-28: The bootloader shall retain a known-good image or equivalent recovery path so an interrupted, invalid, or failed update does not brick the device.
- FR-29: Rev A shall support OTA through its Matter-over-Thread operational network; Rev B may use Thread or Wi-Fi transport. A BLE SMP path may be retained for development and service.

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
- NFR-10: Power loss during flash program, erase, logging, or OTA staging shall not corrupt the active firmware image or prevent recovery.

---

### 5.3 Security
- NFR-6: Wireless communication shall be encrypted.
- NFR-7: Unauthorized firmware installation shall be prevented.
- NFR-11: Sensor-history storage shall not contain credentials, private keys, or other security-sensitive commissioning material.

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
- Dedicated QSPI signals shall connect the MCU to external NOR flash.
- Rev B shall connect the Wi-Fi companion through a separate standard SPI peripheral so the MCU QSPI controller remains dedicated to flash.

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
| Secure update and recovery | Signed-image OTA, rejection, interruption, and rollback tests |
| Sensor-history storage | Capacity, wear, integrity, wraparound, and power-interruption tests |

---

## 9. Future Extensions

- Additional Matter clusters
- Expanded sensor SKUs
- Wi-Fi-only or externally powered variants
