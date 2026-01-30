# MatterSense — Hardware Block Selection / Trade Study

**Purpose:**  
This document captures major hardware block options for MatterSense Rev A and Rev B prior to schematic and PCB design. It evaluates candidate technologies and ICs at a block level and records preliminary direction and decision status.

This document complements the HRS and will evolve as trade studies are completed and decisions are frozen.

---

## 1) MCU / SoC Candidates (Rev A + Rev B)

### Candidate Comparison

| Candidate | Form Factor | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
| --- | --- | --- | --- | --- | --- | --- |
| nRF52840 (Discrete) | SoC | • Lowest BOM cost<br>• Full control over RF/layout<br>• Maximum flexibility | • Requires RF design effort<br>• FCC/IC certification burden<br>• Higher validation and schedule risk | • TX: 4.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 3.16 µA | $3.59 – $5.48 | 1000+ <br>(Mouser & Digi-Key) |
| Raytac MDBT50Q-1M | Module | • Historically low-cost certified module<br>• Compact footprint | • Currently unavailable<br>• Potential lifecycle/EOL risk | • TX: TBD<br>• RX: TBD<br>• Sleep: TBD | $6.00 – $9.00 | 0 <br>(Mouser & Digi-Key) |
| u-blox BMD-340-A-R | Module | • Fully certified (FCC/IC/CE)<br>• Strong vendor documentation<br>• Lower cost than BL654 | • Less field history than BL654 | • TX: 4.8 mA – 14.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 2.35 µA | $7.64 – $9.60 | 1000+ <br>(Mouser & Digi-Key) |
| Ezurio BL654 | Module | • Pre-certified (FCC/IC/CE)<br>• Proven production module<br>• Strong RF performance<br>• Simplifies RF and schedule risk | • Higher BOM vs discrete | • TX: 4.8 mA – 14.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 3.1 µA | $11.63 – $12.08 | 1000+ <br>(Mouser & Digi-Key) |
| ESP32-WROOM-32 (Reference Only) | Module | • Very low cost<br>• Integrated Wi-Fi + BLE<br>• Massive ecosystem | • High idle power<br>• Poor fit for coin-cell operation<br>• Firmware and power model divergence | • TX: 130 mA – 240 mA<br>• RX: 9 mA – 100 mA<br>• Sleep: 10 µA – 0.8 mA | $3.50 – $5.00 | 1000+ <br>(Mouser & Digi-Key) |

### Notes & Considerations

- **Ezurio BL654 is the current leading candidate** due to:
  - Comprehensive pre-certification
  - Proven RF performance in low-power designs
  - Reduced validation and regulatory risk
- **u-blox BMD-340-A-R** is the **primary cost-reduced alternative**:
  - Meaningfully cheaper than BL654
  - Strong availability and documentation
  - Feature parity with discrete nRF52840 confirmed
- **Raytac MDBT50Q-1M** is retained for historical context only:
  - Currently out of stock at major distributors
  - Considered high lifecycle risk for new designs
- **ESP32 modules are included for comparison only**:
  - Idle power and architecture are incompatible with Rev A goals
  - Would significantly complicate low-power firmware design

### Feature Parity (Confirmed for nRF52840-Based Modules)

- NFC pin exposure suitable for OOB pairing
- Flash/RAM capacity sufficient for Matter and future Thread/Zigbee support
- Required peripheral exposure confirmed (GPIO, ADC, I²C, SPI)

**Preliminary Direction:** Ezurio BL654  
**Decision Status:** Tentatively preferred; pending schematic validation

---


## 2) Wi-Fi Companion Candidates (Rev B)

### Candidate Comparison

| Candidate | Form Factor | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
| --- | --- | --- | --- | --- | --- | --- |
| nRF7002 (Discrete) | IC | • Native Nordic ecosystem<br>• Designed as Wi-Fi companion to nRF52<br>• Avoids dependence on module stock | • Requires RF design and antenna tuning<br>• Separate FCC/IC certification effort | • TX: up to ~260 mA (5 GHz)<br>• RX: ~56–60 mA<br>• Sleep: ~15 µA<br>• Shutdown: ~1.7 µA | $2.58 | 1000+ (Digi-Key) |
| MinewSemi MS14SF1-1N02AIR | Module (PCB Antenna) | • Integrated PCB antenna<br>• Sold through Digi-Key (Marketplace)<br>• Good candidate if stock remains available | • Marketplace sourcing (not primary franchised line)<br>• Less established ecosystem vs Fanstel | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $10.00 | 1000+ (Digi-Key) |
| Fanstel WM02F | Module (PCB Trace Antenna) | • Integrated PCB trace antenna<br>• nRF7002 module family is widely referenced<br>• Good “module-first” path for Rev B | • Stock often <1000 at distributors<br>• Lead times can apply depending on variant | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | TBD | <1000 (Digi-Key) |
| Fanstel WM02C | Module (Chip Antenna) | • Integrated chip antenna<br>• Also carried at Mouser<br>• Smaller than WM02F class modules | • Stock often <1000 at distributors<br>• Antenna performance/layout keepouts still matter | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | TBD | <1000 (Mouser & Digi-Key) |
| Combo BLE + Wi-Fi Module (Non-Nordic, Reference Only) | Module | • Single-module integration | • Breaks Nordic-only architecture<br>• High idle power<br>• Firmware and power model divergence | • TX: 200+ mA<br>• Idle: typically mA-range | TBD | 1000+ |

### Notes & Considerations

- Rev B assumes **Wi-Fi is optional and aggressively duty-cycled**:
  - Wi-Fi is **power-gated during battery operation**
  - Wi-Fi is **fully enabled during USB-powered operation**
- **nRF7002 peak TX current (~260 mA)** drives Rev B power design and rules out coin-cell for Wi-Fi use.
- **Module selection constraint:** candidate Wi-Fi modules must include an **integrated antenna** (PCB trace or chip antenna). Modules requiring an external antenna (pads/U.FL) are excluded from the primary shortlist.
- Current nRF7002 module ecosystem shows **limited deep distributor stock**:
  - This is acceptable for a small run (~500–1000 units).
  - For a mass-produced commercial product, limited module stock would likely force either:
    - A different Wi-Fi solution with broader module availability, or
    - Discrete RF design with full certification testing.

### Open Items (Wi-Fi Block)

- Select primary module candidate based on:
  - Sustained distributor availability (prefer Mouser/Digi-Key)
  - Antenna type (PCB trace vs chip antenna) and PCB keepout constraints
- Confirm SDIO vs SPI interface choice and SoC pin impact
- Validate PMIC selection against:
  - ~300 mA peak load and fast transient response
- Define firmware policy for:
  - Battery vs USB Wi-Fi behavior
  - TWT configuration and wake cadence

**Preliminary Direction:** nRF7002 module with integrated antenna (TBD)  
**Decision Status:** TBD – pending final module selection and PMIC selection

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
