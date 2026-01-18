# MatterSense — Hardware Block Selection / Trade Study

**Purpose:**  
This document captures major hardware block options for MatterSense Rev A and Rev B prior to schematic and PCB design. It evaluates candidate technologies and ICs at a block level and records preliminary direction and decision status.

This document complements the HRS and will evolve as trade studies are completed and decisions are frozen.

---

## 1) MCU / SoC Candidates (Rev A + Rev B)

### Candidate Comparison

| Candidate | Form Factor | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|------------|---------------|----------|--------------|-----------------|--------------|
| nRF52840 (Discrete) | SoC | • Lowest BOM cost<br>• Full control over RF/layout<br>• Maximum flexibility | • Requires RF design effort<br>• FCC/IC certification burden<br>• Higher validation risk | Excellent sleep<br>Low BLE TX | $$ | 1000+ (Mouser & Digi-Key) |
| nRF52840 Module (TBD) | Module | • Simplified RF design<br>• Reduced compliance risk<br>• Faster bring-up | • Higher BOM cost<br>• Less layout flexibility<br>• Vendor selection pending | Slightly higher idle | $$$ | TBD |
| Laird BL654 | Module | • Pre-certified (FCC/IC/CE)<br>• Proven production module<br>• Simplifies RF and schedule risk | • Higher BOM vs discrete<br>• Feature parity vs full nRF52840 must be confirmed | TBD (expected slightly higher idle) | $$$ | 1000+ (Mouser & Digi-Key) |

### Notes & Considerations

- The **Laird BL654 is the current leading candidate** due to:
  - Pre-certification reducing regulatory risk
  - Simplified RF layout and validation
  - Proven availability from major distributors
- The discrete **nRF52840 remains a viable fallback** if:
  - Cost becomes the dominant constraint
  - RF design and certification effort is acceptable
- Feature parity items to confirm for BL654:
  - NFC pin availability
  - Flash/RAM capacity suitability for Matter
  - Peripheral availability vs firmware needs

**Preliminary Direction:** Laird BL654  
**Decision Status:** Tentatively preferred; pending feature parity and schematic validation


---

## 2) Wi-Fi Strategy (Rev B)

### Candidate Comparison

| Candidate | Architecture | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|--------------|----------|----------|--------------|-----------------|--------------|
| nRF52840 + nRF70 (Discrete) | Companion IC | Native Nordic ecosystem; Matter alignment | Two-chip solution; layout complexity | High TX peaks; duty-cycled | $$–$$$ | TBD |
| nRF70-based Certified Module | Module | Simplified RF + compliance | Higher BOM; vendor lock-in | Slightly higher idle | $$$ | TBD |

### Notes & Considerations
- Rev B assumes Wi-Fi is duty-cycled aggressively when on battery.
- Hardware must tolerate high peak current during Wi-Fi TX.
- Module vs discrete decision impacts layout complexity and certification effort.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

## 3) Sensor Block Selection

### 3.1 Temperature / Humidity (Required)

#### Candidate Comparison

| Candidate | Interface | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| Sensirion SHTC3 | I²C | Proven; low power; compact | Limited sensing scope | Very low | $$ | High (Mouser) |

### Notes & Considerations
- SHTC3 is the agreed baseline T/H sensor for both revisions.
- Well-suited for intermittent sampling and low-power operation.

**Preliminary Direction:** SHTC3  
**Decision Status:** Pending schematic validation

---

### 3.2 VOC / IAQ (Required)

#### Candidate Comparison

| Candidate | Sensor Class | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|--------------|----------|----------|--------------|-----------------|--------------|
| ENS160 | VOC / IAQ | Purpose-built IAQ sensor | Separate pressure/temp if needed | Moderate | $$ | High (Mouser) |
| BME688 | Multi-sensor | Combines IAQ + env sensing | More complex; heater usage | Moderate–High | $$$ | High (Mouser) |

### Notes & Considerations
- BME688 may reduce sensor count if pressure sensing is desired.
- Heater-based IAQ sensors require careful power budgeting.
- Firmware complexity differs significantly between options.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

### 3.3 Ambient Light (Lux) (Required)

#### Candidate Comparison

| Candidate | Interface | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### Notes & Considerations
- Lux sensing is a frozen requirement.
- Specific sensor selection intentionally deferred.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

### 3.4 Optional: Barometric Pressure

#### Candidate Comparison

| Candidate | Interface | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| BME688 (if selected) | I²C | Already present if chosen | Coupled to IAQ choice | See IAQ | $$$ | High (Mouser) |
| TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### Notes & Considerations
- Pressure sensing may be implicitly satisfied depending on IAQ sensor choice.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

### 3.5 Optional: Sound Level / dB

#### Candidate Comparison

| Candidate | Sensor Type | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|-------------|----------|----------|--------------|-----------------|--------------|
| TBD | TBD | TBD | TBD | TBD | TBD | TBD |

### Notes & Considerations
- Sound sensing is optional and low priority.
- Likely requires microphone + analog front end.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

## 4) Power Management Strategy

### Candidate Comparison

| Revision | Approach | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|--------|----------|----------|----------|--------------|-----------------|--------------|
| Rev A | Coin cell direct supply | Minimal BOM; ultra-low power | Limited peak current | Excellent | $ | High |
| Rev B | LiPo + USB-C PMIC | Flexible power; charging | Added complexity | Supports Wi-Fi peaks | $$ | TBD |

### Notes & Considerations
- Rev A prioritizes simplicity and sleep current.
- Rev B prioritizes robustness under Wi-Fi load and USB operation.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

## 5) Battery Strategy

### Candidate Comparison

| Revision | Chemistry | Capacity | Key Pros | Key Cons | Availability |
|--------|----------|----------|----------|----------|--------------|
| Rev A | Coin cell | TBD | Simple; replaceable | Limited current | High |
| Rev B | LiPo | ~2000 mAh | Long runtime; rechargeable | Requires charging circuit | High |

### Notes & Considerations
- Rev B battery size appears feasible within enclosure constraints.
- Final capacity selection tied to Wi-Fi duty cycle assumptions.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

## 6) Antenna Strategy

### Candidate Comparison

| Approach | RF Scope | Key Pros | Key Cons | Certification Impact | Cost |
|--------|----------|----------|----------|----------------------|------|
| Certified modules | BLE / Wi-Fi | Simplified compliance | Higher BOM | Low risk | $$$ |
| Discrete + PCB antenna | BLE / Wi-Fi | Lower BOM; flexibility | RF risk | Higher effort | $$ |

### Notes & Considerations
- Early revisions favor reduced regulatory risk.
- Final antenna tuning deferred beyond this document.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

## 7) Programming & Test Strategy

### Candidate Comparison

| Approach | Use Case | Key Pros | Key Cons | Cost Impact | Notes |
|--------|----------|----------|----------|-------------|-------|
| SWD interface | Dev / debug | Required; standard | Requires access points | Low | Mandatory |
| Bed-of-nails | Manufacturing | Scalable; fast | Fixture cost | Medium | Required |
| Tag-Connect / proprietary | Dev only | Compact | Expensive cables | High | Avoid if possible |

### Notes & Considerations
- Explicit goal to avoid expensive proprietary programming solutions.
- Test point-based access preferred for both dev and production.

**Preliminary Direction:** TBD  
**Decision Status:** TBD

---

## 8) Summary & Next Steps

- This document will be updated as each block is reviewed and frozen.
- Once a block decision is finalized:
  - Preliminary Direction will be locked
  - Decision Status updated
  - HRS and schematic assumptions updated accordingly
