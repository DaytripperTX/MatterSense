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
with a Matter-over-Thread sensor node commissioned over Bluetooth Low Energy and
expanding toward direct Wi-Fi IP transport without requiring a Thread Border Router.

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
- Matter-over-Thread operation with BLE commissioning
- Temperature/humidity, VOC/IAQ/eCO₂, barometric pressure, and lux sensing
- Ultra-low-power operation
- Regulated CR2477 coin-cell power
- 64-Mbit external QSPI NOR flash for secure OTA staging and sensor-data buffering
- SWD-based development and debugging

### Planned Revision B
- Matter-over-Wi-Fi target using a Fanstel WM02C/nRF7002 companion
- nRF5340-class host/module selection required before Rev B schematic capture; the Rev A BL654/nRF52840 is not a currently supported Nordic Matter-over-Wi-Fi host
- Matter-over-Thread remains a separate Rev B build/population option when Wi-Fi is omitted
- NFC-assisted commissioning
- USB-C plus protected 2000 mAh LiPo power
- Operation without a Thread Border Router; a Matter controller/fabric is still required
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
- ✔ Product, system, hardware, and traceability requirements aligned
- ⏳ Rev A schematic capture
- ⏳ Rev B Matter-over-Wi-Fi host/module selection
- ⏳ Firmware and hardware bring-up

---

## Hardware Platform

- **Rev A SoC module:** Ezurio BL654, orderable part 451-00001
- **Rev B host:** nRF5340-class MCU/module to be selected for the Matter-over-Wi-Fi requirement
- **Sensors:** SHTC3, BME688, and VEML7700 in both baseline revisions
- **External memory:** Macronix MX25R6435FZNIL0, 64-Mbit QSPI NOR
- **Power:** Regulated CR2477 in Rev A; USB-C/protected 1S LiPo in Rev B
- **Debug:** SWD
- **Connectivity:** Matter-over-Thread with BLE commissioning in Rev A; Wi-Fi added in Rev B
- **Commissioning:** BLE in both revisions, with NFC assistance planned for Rev B

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
