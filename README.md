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
with a BLE-based sensor node and expanding toward hub-less Wi-Fi operation.

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
- Nordic nRF52840-based BLE SoC
- Temperature & humidity sensing
- BLE commissioning
- Matter protocol support
- Ultra-low-power operation
- Battery-powered design
- SWD-based development and debugging

### Planned Revision B
- Wi-Fi connectivity (nRF70 series)
- NFC-assisted commissioning
- Hub-less operation
- Expanded sensor suite (e.g. VOC / air quality)
- Further power optimization

---

## Repository Structure

```text
MatterSense/
├── docs/
│   ├── PRD.md              # Product Requirements Document
│   ├── SRS.md              # Software Requirements Specification
│   └── architecture/       # Block diagrams, system overviews
│
├── firmware/
│   ├── rev_a/              # Firmware for Revision A hardware
│   └── common/             # Shared code and utilities
│
├── hardware/
│   ├── rev_a/
│   │   ├── schematics/
│   │   ├── pcb/
│   │   └── fabrication/
│   └── rev_b/              # Future hardware revision
│
├── tools/                  # Scripts, utilities, test tools
├── LICENSE
└── README.md
```

> Folder structure mirrors professional embedded product repositories used in
commercial hardware programs.

---

## Development Status

- ✔ System architecture defined
- ✔ Component selection (Rev A) completed
- ✔ PRD & SRS in progress
- ⏳ Firmware bring-up
- ⏳ Hardware schematic & PCB layout

---

## Hardware Platform

- **Primary SoC:** Nordic nRF52840
- **Sensors:** Digital temperature & humidity sensor (Rev A)
- **Power:** Battery-powered (final chemistry TBD)
- **Debug:** SWD
- **Commissioning:** BLE (Rev A), NFC planned (Rev B)

---

## Firmware & Software

- Embedded firmware targeting low-power operation
- Matter protocol stack integration
- OTA update support planned
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
