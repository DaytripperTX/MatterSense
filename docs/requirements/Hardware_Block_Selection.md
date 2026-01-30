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
| Fanstel WM02F | Module (PCB Trace Antenna) | • Integrated PCB trace antenna<br>• Attractive cost when sourced direct<br>• Module-first path for Rev B | • Not stocked at Mouser/Digi-Key (10+ week LT; MOQ 1000)<br>• Reliance on vendor-direct inventory | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $4.20 – $5.03 (Fanstel direct) | ~600 (Fanstel direct)<br>0 (Mouser & Digi-Key) |
| Fanstel WM02C | Module (Chip Antenna) | • Integrated chip antenna<br>• Stocked at major distributors<br>• Good near-term buyable option | • Stock typically <1000 (risk for scaling)<br>• Antenna performance/layout keepouts still matter | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $9.09 – $9.65 | 454 (Digi-Key)<br>320 (Mouser) |
| Fanstel WT02E40E (Combo) | Module (Integrated Antenna) | • Single-module BLE + Wi-Fi integration<br>• Simplifies routing and integration<br>• Reduces interconnect complexity | • Higher cost than WM02C/WM02F<br>• Alternate architecture vs separate SoC + Wi-Fi companion | • TX: Wi-Fi up to ~260 mA<br>• RX: ~56–60 mA<br>• Sleep: TBD | $13.99 (Fanstel direct) | ~300 (Fanstel direct) |

### Notes & Considerations

- Rev B assumes **Wi-Fi is optional and aggressively duty-cycled**:
  - Wi-Fi is **power-gated during battery operation**
  - Wi-Fi is **fully enabled during USB-powered operation**
- **nRF7002 peak TX current (~260 mA)** drives Rev B power design and rules out coin-cell for Wi-Fi use.
- **Module selection constraint:** Wi-Fi modules must include an **integrated antenna** (PCB trace or chip antenna).
- **Supply chain reality (acceptable for this project scope):**
  - For a small run (likely **<500**, possibly **<1000**), sub-1000 stock is acceptable.
  - For mass production, limited module stock would likely force either:
    - A different Wi-Fi solution with broader module availability, or
    - Discrete RF design with full certification testing.
  - Distributor availability may fluctuate, and restocks may occur periodically (e.g., Digi-Key may show future replenishment dates).
    - For larger builds, procurement would likely involve coordinating directly with Fanstel to:
      - Confirm lead times and production availability for 500–1000+ quantities
      - Potentially reduce unit cost vs distributor single-quantity pricing
      - Obtain clearer lifecycle visibility (PCN/EOL timing) to reduce long-term risk
- **Optional population (DNP) strategy:**
  - The PCB shall be designed such that the Wi-Fi module can be **left unpopulated** (DNP) and the device remains fully functional in a BLE/Thread/ZigBee-only mode.
  - Firmware shall detect the absence of the Wi-Fi module (or treat it as unavailable) and operate without Wi-Fi features enabled.
  - This reduces procurement risk and BOM cost for builds where Wi-Fi is not required.

### Open Items (Wi-Fi Block)

- Confirm SDIO vs SPI interface choice and SoC pin impact
- Validate PMIC selection against:
  - ~300 mA peak load and fast transient response
- Define firmware policy for:
  - Battery vs USB Wi-Fi behavior
  - TWT configuration and wake cadence

### Closed Items (Wi-Fi Block)

- Integrated antenna requirement captured (modules requiring external antennas excluded from primary shortlist)
- Wi-Fi optional population (DNP) strategy captured:
  - System must function fully in BLE/Thread/ZigBee-only mode when Wi-Fi is unpopulated
- Wi-Fi peak current / power-state constraints captured (~260 mA TX; sleep/shutdown and TWT targets)
- Primary Wi-Fi module candidate selected: **Fanstel WM02C**
  - Rationale: in-stock at major distributors; integrated chip antenna; sufficient quantity for project-scale runs

**Preliminary Direction:** Fanstel WM02C (nRF7002 module with integrated chip antenna)  
**Decision Status:** Frozen for Rev B (revisit if availability/cost changes)


---

## 3) Sensor Block Selection

### 3.1 Temperature / Humidity (Required)

#### Candidate Comparison

| Candidate | Interface | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| Sensirion SHTC3 | I²C | • Proven baseline choice<br>• Small DFN (2×2 mm)<br>• Good low-power modes | • Older-gen vs SHT4x family<br>• “Idle state” current notable unless put to sleep | • Measurement avg: 430 µA typ (normal), 270 µA typ (low-power)<br>• Sleep: 0.3 µA typ<br>• Avg @ 1 Hz: 4.9 µA (normal), 0.5 µA (low-power) | $2.06 – $2.70 | 1000+ (Mouser & Digi-Key) |
| Sensirion SHT40 (SHT4x family) | I²C | • Lower cost than SHTC3 at 1pc<br>• Very low idle current<br>• Newer-gen platform | • Would change the “agreed baseline” part<br>• Different commands / timing vs SHTC3 (firmware impact) | • Idle: 80 nA<br>• Avg @ 1 Hz: 0.4 µA | $1.80 – $1.80 | 1000+ (Mouser & Digi-Key) |
| TI HDC2010 | I²C | • Extremely low sleep current<br>• Very low average current at periodic sampling | • WLCSP package (assembly/handling complexity)<br>• Different driver ecosystem vs Sensirion | • Sleep: 50 nA<br>• Avg @ 1 Hz: 550 nA (RH+T, 11-bit) | $1.58 – $1.58 | 1000+ (Mouser & Digi-Key) |

### Notes & Considerations
- **SHTC3 remains the agreed baseline** T/H sensor for both revisions.
- SHTC3 must be explicitly placed into **Sleep Mode** between measurements to avoid higher “Idle state” current.
- **SHT40** is a credible cost/power alternative if we ever decide to move off SHTC3 (firmware change required).
- **HDC2010** looks excellent on paper for power, but the **WLCSP package** is the main integration risk.

**Preliminary Direction:** SHTC3  
**Decision Status:** Pending schematic validation

---

### 3.2 VOC / IAQ (Required)

#### Candidate Comparison

| Candidate | Sensor Class | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|--------------|----------|----------|--------------|-----------------|--------------|
| [ENS160](https://www.sciosense.com/wp-content/uploads/2023/12/ENS160-Datasheet.pdf) | VOC / IAQ | • On-sensor IAQ outputs (TVOC + eCO2 eq)<br>• Simple host integration | • Needs T/H compensation input<br>• Higher active current | • Sleep: 0.01 mA<br>• Idle: ~2–2.5 mA<br>• Active: ~29 mA avg (Std) | $5.63 – $5.63 | 1000+ ([Mouser](https://www.mouser.com/c/sensors/environmental-sensors/?series=ENS160) & [Digi-Key](https://www.digikey.com/en/products/detail/sciosense/ENS160-BGLT/16129831)) |
| [BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | Multi-sensor | • IAQ + T/H + Pressure in one<br>• Flexible profiles | • BSEC integration typical<br>• Heater power tradeoffs | • Sleep: 0.15 µA<br>• ULP IAQ: ~90 µA avg<br>• Std gas: ~3.9 mA avg | $8.65 – $8.65 | [Digi-Key](https://www.digikey.com/en/products/detail/bosch-sensortec/BME688/13681261): 10,000+<br>[Mouser](https://www.mouser.com/ProductDetail/Bosch-Sensortec/BME688?qs=IS%252B4QmGtzzqQoVDscqwx3A%3D%3D): <1000 (6,000 exp 3/3/2026) |

### Notes & Considerations
- Both are heater-based; duty-cycling is expected.
- ENS160 is simpler on the host; BME688 can reduce sensor count.

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
