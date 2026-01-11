# Requirements Traceability Matrix (RTM)
## MatterSense Environmental Sensor Node

> This document maps product-level requirements to system requirements,
> implementation domains, and verification methods. It supports requirements
> coverage, impact analysis, and disciplined system development.
>
> This is a living document and will evolve as requirements and implementation
> mature.

---

## 1. Purpose

The Requirements Traceability Matrix (RTM) provides end-to-end traceability from
product intent through system requirements and verification. It ensures that all
defined product requirements are addressed by engineering requirements and that
each requirement can be validated.

---

## 2. Traceability Overview

The RTM maps:

`PRD Requirement → SRS Requirement → Implementation Domain → Verification Method`

---

## 3. Traceability Matrix

| PRD ID | PRD Requirement | SRS ID(s) | Implementation Domain | Verification Method |
|------|------------------|-----------|------------------------|---------------------|
| PRD-1 | Temperature & humidity monitoring | FR-1, FR-2 | Sensor HW + Firmware | Functional test |
| PRD-2 | eCO₂ / VOC monitoring | FR-3 | Sensor HW + Firmware | Functional test |
| PRD-3 | Ambient light (lux) monitoring | FR-4 | Sensor HW + Firmware | Functional test |
| PRD-4 | Optional barometric pressure monitoring | FR-5 | Optional Sensor HW + Firmware | Functional test |
| PRD-5 | Optional ambient sound level (dB) monitoring | FR-6 | Optional Sensor HW + Firmware | Functional test |
| PRD-6 | Configurable sensor sampling | FR-7 | Firmware | Configuration test |
| PRD-7 | Matter-compatible data reporting | FR-8, FR-9 | Firmware + Protocol Stack | Interoperability test |
| PRD-8 | Conditional exposure of optional sensors | FR-10 | Firmware | Feature detection test |
| PRD-9 | BLE-based commissioning | FR-11, FR-12 | Wireless FW | Commissioning test |
| PRD-10 | Wi-Fi connectivity (Rev B) | FR-13 | Wireless HW + FW | Connectivity test |
| PRD-11 | Battery-powered operation | FR-19, FR-20 | Power HW + FW | Power profiling |
| PRD-12 | Multi-month battery life | NFR-1, NFR-2 | Power HW + FW | Long-duration profiling |
| PRD-13 | USB-powered operation (Rev B) | FR-14, FR-17 | Power HW + FW | Power-mode test |
| PRD-14 | Power-mode–aware radio behavior | FR-15, FR-16, FR-18 | Firmware | Mode transition test |
| PRD-15 | Low-power sleep-centric design | FR-19 | Firmware | Current measurement |
| PRD-16 | Robust recovery from power events | FR-21 | Firmware | Fault injection test |
| PRD-17 | Secure firmware updates | FR-22, FR-23 | Firmware + Bootloader | OTA update test |
| PRD-18 | Secure wireless communication | NFR-6 | Wireless FW | Security validation |
| PRD-19 | Modular, revision-aware firmware | NFR-8, NFR-9 | Firmware architecture | Code review |
| PRD-20 | Privacy-preserving sound sensing | FR-6, PRD Notes | Firmware + Product Design | Design review |

---

## 4. Notes on Optional Features

Optional sensors (barometric pressure and sound level) are treated as conditional
features. Their associated system requirements are only applicable when the
corresponding hardware is populated. Firmware shall detect and expose these
capabilities dynamically.

---

## 5. Change Impact Guidance

- Changes to PRD requirements shall trigger review of associated SRS entries.
- Changes to SRS requirements shall trigger review of affected firmware and
  hardware domains.
- New hardware revisions shall update this matrix to preserve traceability.

---

## 6. Usage

This RTM is intended to be used during:
- Architecture definition
- Design reviews
- Verification planning
- Portfolio and technical evaluation

---

## 7. Limitations

This RTM does not replace detailed test plans or manufacturing validation
documentation. It provides traceability and coverage, not execution detail.

