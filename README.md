# MatterSense

⚠️ **Proprietary Project – Not Open Source**

MatterSense is a personal portfolio project developed and executed as if it were
a professional, mass-manufactured product. While this repository is publicly
viewable for evaluation and demonstration purposes, all contents are proprietary
and protected. See the LICENSE file for full terms.

---

## Overview

**MatterSense** is a low-power environmental sensor node designed for modern smart
home ecosystems using the **Matter** protocol. The project emphasizes professional
hardware and firmware development practices, with a strong focus on:

- Standards-based interoperability
- Low-power embedded design
- Scalable hardware revisions
- Design-for-manufacturing (DFM)
- Clear documentation and requirements traceability

The system is architected to evolve across multiple hardware revisions, beginning
with a Matter-over-Thread sensor node that also supports Bluetooth Low Energy (BLE)
commissioning and standalone local operation, then expanding toward direct Wi-Fi IP
transport. A Thread Border Router is required for Matter-over-Thread, but not for
the device's non-Matter BLE Local Mode.

---

## Project Goals

- Design a battery-powered environmental sensor suitable for mass manufacture
- Implement Matter-compatible device behavior
- Follow professional product development workflows (PRD, SRS, revisioning)
- Demonstrate embedded systems, PCB design, and system architecture skills
- Maintain strict control of intellectual property

---

## Key Features

### Revision A (Current Focus)
- Ezurio BL654 module based on the Nordic nRF52840
- Matter-over-Thread operation, BLE commissioning, and secure BLE Local Mode
- Direct sensor access and selected local configuration over BLE when no Thread Border Router is available
- Temperature/humidity, VOC/IAQ/eCO₂, barometric pressure, and lux sensing
- Ultra-low-power operation
- Regulated CR2477 coin-cell power
- 64-Mbit external QSPI NOR flash for secure OTA staging and sensor-data buffering
- SWD-based development and debugging

### Planned Revision B
- Matter-over-Wi-Fi target using a Fanstel WT02C40C nRF5340+nRF7002 combo module with integrated BLE/Thread and Wi-Fi antennas
- Lower module/BOM count than the separate BL654+WM02C path, with an internal nRF7002 power switch and reference-like Nordic interconnect
- 64-Mbit external serial NOR flash operated over SPIM4; the combo module exposes the nRF5340 QSPI signal nets, but they are the same bus and chip-select connection used internally by nRF7002
- Matter-over-Thread remains a separate Rev B build using nRF7002 hard-off or the footprint-compatible BT40F nRF5340-only population
- BLE Local Mode remains available independently of Thread or Wi-Fi infrastructure
- NFC-assisted commissioning
- USB-C power/charging plus USB 2.0 device data for service, recovery, and wired firmware updates
- Operation without a Thread Border Router through BLE Local Mode or Matter-over-Wi-Fi; a Matter controller/fabric is required only for Matter operation
- The same baseline environmental sensor suite as Rev A, with room for future variants

---

## Repository Structure

```text
MatterSense/
├── docs/
│   ├── requirements/       # PRD, SRS, HRS, RTM, and block selections
│   └── architecture/       # System and power architecture
├── LICENSE
└── README.md
```

> Folder structure mirrors professional embedded product repositories used in
commercial hardware programs.

---

## Development Status

- ✔ System architecture defined
- ✔ Baseline component and power-architecture selection completed
- ✔ Product, system, hardware, and traceability requirements aligned for schematic capture
- ⏳ Rev A schematic capture
- ⏳ Rev B WT02C40C schematic integration and measured power validation
- ⏳ Firmware and hardware bring-up

---

## Hardware Platform

- **Rev A SoC module:** Ezurio BL654, orderable part 451-00001
- **Rev B radio/host:** Fanstel WT02C40C combo module with nRF5340+nRF7002 and two integrated chip antennas
- **Sensors:** SHTC3, BME688, and VEML7700 in both baseline revisions
- **External memory:** Macronix MX25R6435FZNIL0, 64-Mbit serial NOR; QSPI in Rev A and SPIM4 in Rev B
- **Power:** Regulated CR2477 in Rev A; USB-C/protected 1S LiPo in Rev B
- **Wired service:** Rev B USB 2.0 device interface for logs, recovery, and wired firmware updates
- **Debug:** SWD
- **Connectivity:** Matter-over-Thread plus BLE Local Mode in Rev A; Wi-Fi added in Rev B
- **Commissioning:** BLE in both revisions, with NFC assistance planned for Rev B; BLE Local Mode is a separate non-Matter operational interface

---

## Firmware & Software

- Embedded firmware targeting low-power operation
- Matter protocol stack integration
- Signed OTA updates staged in external flash, with rollback/recovery behavior
- Structured for long-term maintainability

---

## Intellectual Property Notice

This repository is **not open source**.

All source code, hardware designs, schematics, PCB layouts, documentation,
and related materials are proprietary and may not be copied, modified,
manufactured, distributed, or used without explicit written permission.

This repository is made public **solely for portfolio and evaluation purposes**.

---

## Author

**Chad Nelson**  
Electrical Engineer – Embedded Systems & PCB Design

---

## License

See the `LICENSE` file for full details.
