# System Requirements Specification (SRS)
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
SoC. Rev A supports Matter-over-Thread, BLE commissioning, and a product-specific
BLE Local Mode that does not require a Thread Border Router or Matter controller.
Rev B uses an nRF5340+nRF7002 combo module for Matter-over-Wi-Fi operation. Both
revisions include environmental sensors and external nonvolatile storage.

The system supports multiple power modes that directly influence wireless behavior
and system availability.

---

## 3. System Architecture

### 3.1 Hardware
- Rev A SoC: Nordic nRF52840 in the Ezurio BL654 module
- Rev B host/radio: Fanstel WT02C40C combo module integrating nRF5340+nRF7002 and both radio antennas
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
  - 64-Mbit external serial NOR for OTA staging and selected sensor history; QSPI on Rev A and SPIM4 on Rev B
- Debug: SWD interface
- Expansion: NFC pins routed for Rev B compatibility

---

### 3.2 Wireless Connectivity
- Rev A:
  - Bluetooth Low Energy commissioning
  - Secure, non-Matter BLE Local Mode for sensor access and selected configuration
  - Matter-over-Thread operational transport
- Rev B:
  - Bluetooth Low Energy commissioning
  - Secure, non-Matter BLE Local Mode independent of Thread or Wi-Fi availability
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
- FR-6: A future externally powered hardware variant may sample non-recording ambient sound level (dB) data; this function shall be disabled and unexposed when a microphone is not populated.
- FR-7: Sensor sampling intervals shall be configurable at build time or firmware configuration time.
- FR-32: Product temperature and relative-humidity values shall use the SHTC3 as the authoritative measurement source. BME688 temperature and humidity data shall be used for gas-sensor compensation and may be exposed only as explicitly identified diagnostic data.

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
- FR-13: Rev B shall support Matter-over-Wi-Fi through the nRF7002 integrated in WT02C40C.
- FR-24: Both revisions shall support Matter-over-Thread as a Sleepy End Device or Matter Intermittently Connected Device appropriate to the final application behavior; it is Rev A's normal transport and a separate Rev B firmware/build option using WT02C40C with Wi-Fi disabled or the footprint-compatible BT40F Thread-only population.
- FR-30: Both revisions shall provide a BLE Local Mode that exposes current sensor readings and selected local configuration or service operations through a product-specific GATT interface without requiring a Thread Border Router, Wi-Fi access point, Matter controller, or Matter fabric.
- FR-31: BLE Local Mode shall remain distinct from Matter commissioning and shall not be represented as Matter-over-BLE. Its advertising policy may be low-duty, user-initiated, or time-limited to meet the battery-life requirement, but the mode shall be available without reflashing firmware.

---

### 4.4 Power-Mode–Aware Operation (Rev B)
- FR-14: The system shall detect whether it is battery-powered or externally powered.
- FR-15: When battery-powered, the system shall use transport-appropriate low-power behavior. Radios not required by the selected build or operating mode may be hard-off; an active Matter-over-Wi-Fi configuration shall remain associated as required by FR-16.
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
- FR-27: OTA images shall be staged in the external serial NOR secondary slot and authenticated before activation.
- FR-28: The bootloader shall retain a known-good image or equivalent recovery path so an interrupted, invalid, or failed update does not brick the device.
- FR-29: Rev A shall support OTA through its Matter-over-Thread operational network; Rev B may use Thread or Wi-Fi transport. A BLE SMP path may be retained for development and service.
- FR-33: Rev B shall provide a USB 2.0 device interface for service logs, recovery, and wired firmware-update workflows in addition to USB-C power and charging.

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
- NFR-12: BLE Local Mode shall require authenticated and encrypted access for sensor readings, configuration, stored history, and service actions; only discovery/advertising metadata explicitly classified as public may be exposed before authentication.
- NFR-13: Device identity, attestation material, private keys, and commissioning credentials shall use protected internal SoC storage and controlled factory provisioning. The baseline hardware shall not require an external secure element.

---

### 5.4 Maintainability
- NFR-8: Firmware shall be modular and revision-aware.
- NFR-9: Hardware revision changes shall minimize firmware rework.

---

## 6. Interface Requirements

### 6.1 Debug Interface
- SWD shall be accessible for development and test.

### 6.2 External Interfaces
- NFC interface pins shall be routed and electrically supported for NFC-assisted Matter onboarding.
- Test points shall be provided for power and ground.
- Rev A dedicated QSPI signals shall connect the MCU to external NOR flash.
- Rev B shall connect external NOR flash in standard SPI mode through SPIM4. WT02C40C exposes the nRF5340 QSPI signal nets, but those same nets and the sole dedicated QSPI CSN are connected internally to nRF7002 and do not provide a second independently selectable QSPI bus.
- Rev B USB-C D+ and D− shall connect to the WT02C40C/nRF5340 USB device interface with USB 2.0 routing, ESD protection, and VBUS sensing appropriate to the selected receptacle and power path.
- Both revisions shall provide one populated multifunction user button for commissioning activation, BLE Local Mode activation, and factory-reset input; exact press behavior shall be defined by firmware.

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
| BLE Local Mode | No-infrastructure GATT access, security, advertising-policy, and power testing |
| Power-mode switching | Mode transition testing |
| Secure update and recovery | Signed-image OTA, rejection, interruption, and rollback tests |
| Sensor-history storage | Capacity, wear, integrity, wraparound, and power-interruption tests |
| USB device interface | Enumeration, service/recovery access, ESD design review, and wired-update test |
| Credential protection | Provisioning, readout-protection, debug-lock, and unauthorized-access test |

---

## 9. Future Extensions

- Additional Matter clusters
- Expanded sensor SKUs
- Wi-Fi-only or externally powered variants
