# MatterSense — Hardware Block Selection / Trade Study

**Purpose:**  
This document captures major hardware block options for MatterSense Rev A and Rev B prior to schematic and PCB design. It evaluates candidate technologies and ICs at a block level and records preliminary direction and decision status.

This document complements the HRS and will evolve as trade studies are completed and decisions are frozen.

---

## 1) MCU / SoC Candidates (Rev A + Rev B)

### Candidate Comparison

| Candidate | Form Factor | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
| --- | --- | --- | --- | --- | --- | --- |
| [nRF52840 (Discrete)](https://docs-be.nordicsemi.com/bundle/ps_nrf52840/attach/nRF52840_PS_v1.11.pdf?_LANG=enus) | SoC | • Lowest BOM cost<br>• Full control over RF/layout<br>• Maximum flexibility | • Requires RF design effort<br>• FCC/IC certification burden<br>• Higher validation and schedule risk | • TX: 4.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 3.16 µA | $3.59 – $5.48 | 1000+ <br>([Mouser](https://www.mouser.com/c/semiconductors/wireless-rf-integrated-circuits/rf-system-on-a-chip-soc/?m=Nordic%20Semiconductor&series=nRF52840) & [Digi-Key](https://www.digikey.com/en/products/detail/nordic-semiconductor-asa/NRF52840-QIAA-R7/11200025)) |
| [Raytac MDBT50Q-1M](https://www.mouser.com/datasheet/2/744/_5bnRF52840_5d_MDBT50Q_1MV2__26_MDBT50Q_P1MV2_Ver_-2525658.pdf?srsltid=AfmBOorB5Z35m8gI5794esgzA8Nj53-aUOLUO8bO6VV-565TyayQimsI) | Module | • Historically low-cost certified module<br>• Compact footprint | • Currently unavailable<br>• Potential lifecycle/EOL risk | • TX: TBD<br>• RX: TBD<br>• Sleep: TBD | $6.00 – $9.00 | 0 <br>([Mouser](https://www.mouser.com/c/?q=MDBT50Q) & [Digi-Key](https://www.digikey.com/en/products/detail/raytac/MDBT50Q-1MV2/13677591)) |
| [u-blox BMD-340-A-R](https://content.u-blox.com/sites/default/files/BMD-340_DataSheet_UBX-19033353.pdf) | Module | • Fully certified (FCC/IC/CE)<br>• Strong vendor documentation<br>• Lower cost than BL654 | • Less field history than BL654 | • TX: 4.8 mA – 14.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 2.35 µA | $7.64 – $9.60 | 1000+ <br>([Mouser](https://www.mouser.com/ProductDetail/u-blox/BMD-340-A-R?qs=wd5RIQLrsJgGcI5R13BUYg%3D%3D&srsltid=AfmBOorkcgR2_ZodEhfTeudAxMhkFS4pwKuMG93ztHSLkbOvfiFKmboP) & [Digi-Key](https://www.digikey.com/en/products/detail/u-blox/BMD-340-A-R/8638939)) |
| [Ezurio BL654](https://www.ezurio.com/documentation/datasheet-bl654) | Module | • Pre-certified (FCC/IC/CE)<br>• Proven production module<br>• Strong RF performance<br>• Simplifies RF and schedule risk | • Higher BOM vs discrete | • TX: 4.8 mA – 14.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 3.1 µA | $11.63 – $12.08 | 1000+ <br>([Mouser](https://www.mouser.com/ProductDetail/Ezurio/451-00001?qs=MLItCLRbWszU6GB3nrHvkA%3D%3D&srsltid=AfmBOorA1ltk7Dfq18QRUn1tKwWPF2PeBid5a2opnczd6jA04DZWorvP) & [Digi-Key](https://www.digikey.com/en/products/detail/ezurio/451-00001/9172334)) |
| [ESP32-WROOM-32 (Reference Only)](https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32_datasheet_en.pdf) | Module | • Very low cost<br>• Integrated Wi-Fi + BLE<br>• Massive ecosystem | • High idle power<br>• Poor fit for coin-cell operation<br>• Firmware and power model divergence | • TX: 130 mA – 240 mA<br>• RX: 9 mA – 100 mA<br>• Sleep: 10 µA – 0.8 mA | $3.50 – $5.00 | 1000+ <br>([Mouser](https://www.mouser.com/ProductDetail/Espressif-Systems/ESP32-WROOM-32E-N4?qs=Li%252BoUPsLEnsPzTWsi%252BRMgQ%3D%3D&srsltid=AfmBOooi9ADzgFX94NQYgr2DWTCfaqRWz4_b8t2TaKm5tqPpJovUdi3F) & [Digi-Key](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-WROOM-32-N4/8544298)) |


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
| [nRF7002 (Discrete)](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/6470/NRF7002-QFAA-R.pdf) | IC | • Native Nordic ecosystem<br>• Designed as Wi-Fi companion to nRF52<br>• Avoids dependence on module stock | • Requires RF design and antenna tuning<br>• Separate FCC/IC certification effort | • TX: up to ~260 mA (5 GHz)<br>• RX: ~56–60 mA<br>• Sleep: ~15 µA<br>• Shutdown: ~1.7 µA | $2.58 | 1000+ ([Digi-Key](https://www.digikey.com/en/products/detail/nordic-semiconductor-asa/NRF7002-QFAA-R/17738488)) |
| [MinewSemi MS14SF1-1N02AIR](https://en.minewsemi.com/file/MS14SF1-nRF7002_Datasheet_K_EN.pdf) | Module (PCB Antenna) | • Integrated PCB antenna<br>• Sold through Digi-Key (Marketplace)<br>• Good candidate if stock remains available | • Marketplace sourcing (not primary franchised line)<br>• Less established ecosystem vs Fanstel | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $10.00 | 1000+ ([Digi-Key](https://www.digikey.com/en/products/detail/minewsemi/MS14SF1-1N02AIR/26409766)) |
| [Fanstel WM02F](https://static1.squarespace.com/static/561459a2e4b0b39f5cefa12e/t/672e447ee28d49742366de31/1731085441199/WM02C%2BProduct%2BSpecifications.pdf) | Module (PCB Trace Antenna) | • Integrated PCB trace antenna<br>• Attractive cost when sourced direct<br>• Module-first path for Rev B | • Not stocked at Mouser/Digi-Key (10+ week LT; MOQ 1000)<br>• Reliance on vendor-direct inventory | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $4.20 – $5.03 ([Fanstel direct](https://www.fanstel.com/buy/bt840f-v1-nrf52840-bluetooth-5-thread-zigbee-module-wah32-amhmr-6meyb-hlhel)) | ~600 ([Fanstel direct](https://www.fanstel.com/buy/bt840f-v1-nrf52840-bluetooth-5-thread-zigbee-module-wah32-amhmr-6meyb-hlhel))<br>0 ([Mouser](https://www.mouser.com/ProductDetail/Fanstel/WM02F?qs=ST9lo4GX8V39vk8QtDIbrQ%3D%3D) & [Digi-Key](https://www.digikey.com/en/products/detail/fanstel-corp/WM02F/22107923)) |
| [Fanstel WM02C](https://static1.squarespace.com/static/561459a2e4b0b39f5cefa12e/t/672e447ee28d49742366de31/1731085441199/WM02C%2BProduct%2BSpecifications.pdf) | Module (Chip Antenna) | • Integrated chip antenna<br>• Stocked at major distributors<br>• Good near-term buyable option | • Stock typically <1000 (risk for scaling)<br>• Antenna performance/layout keepouts still matter | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $9.09 – $9.65 | 454 ([Digi-Key](https://www.digikey.com/en/products/detail/fanstel-corp/WM02C/22107887))<br>320 ([Mouser](https://www.mouser.com/ProductDetail/Fanstel/WM02C?qs=ST9lo4GX8V2bfCVo1J5PEw%3D%3D)) |
| [Fanstel WT02E40E (Combo)](https://www.mouser.com/datasheet/2/915/WT02E40E_2bProduct_2bSpecifications-3576516.pdf) | Module (Integrated Antenna) | • Single-module BLE + Wi-Fi integration<br>• Simplifies routing and integration<br>• Reduces interconnect complexity | • Higher cost than WM02C/WM02F<br>• Alternate architecture vs separate SoC + Wi-Fi companion | • TX: Wi-Fi up to ~260 mA<br>• RX: ~56–60 mA<br>• Sleep: TBD | $13.99 ([Fanstel direct](https://www.fanstel.com/buy/bt840f-v1-nrf52840-bluetooth-5-thread-zigbee-module-wah32-amhmr-6meyb-775sx-3jcdd)) | ~300 ([Fanstel direct](https://www.fanstel.com/buy/bt840f-v1-nrf52840-bluetooth-5-thread-zigbee-module-wah32-amhmr-6meyb-775sx-3jcdd)) |


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
| [Sensirion SHTC3](https://sensirion.com/resource/datasheet/shtc3) | I²C | • Proven baseline choice<br>• Small DFN (2×2 mm)<br>• Good low-power modes | • Older-gen vs SHT4x family<br>• “Idle state” current notable unless put to sleep | • Measurement avg: 430 µA typ (normal), 270 µA typ (low-power)<br>• Sleep: 0.3 µA typ<br>• Avg @ 1 Hz: 4.9 µA (normal), 0.5 µA (low-power) | $2.06 – $2.70 | 1000+ ([Mouser](https://www.mouser.com/ProductDetail/Sensirion/SHTC3?qs=y6ZabgHbY%252Bx3LlA87fqBwg%3D%3D) & [Digi-Key](https://www.digikey.com/en/products/detail/sensirion-ag/SHTC3-TR-2-5KS/8628148)) |
| [Sensirion SHT40 (SHT4x family)](https://sensirion.com/media/documents/33FD6951/6555C40E/Sensirion_Datasheet_SHT4x.pdf) | I²C | • Lower cost than SHTC3 at 1pc<br>• Very low idle current<br>• Newer-gen platform | • Would change the “agreed baseline” part<br>• Different commands / timing vs SHTC3 (firmware impact) | • Idle: 80 nA<br>• Avg @ 1 Hz: 0.4 µA | $1.80 – $1.80 | 1000+ ([Mouser](https://www.mouser.com/ProductDetail/Sensirion/SHT40-AD1B-R2?qs=zW32dvEIR3scTT6A4VuzaQ%3D%3D) & [Digi-Key](https://www.digikey.com/en/products/detail/sensirion-ag/SHT40-AD1B-R2/13532084)) |
| [TI HDC2010](https://www.ti.com/lit/gpn/HDC2010) | I²C | • Extremely low sleep current<br>• Very low average current at periodic sampling | • WLCSP package (assembly/handling complexity)<br>• Different driver ecosystem vs Sensirion | • Sleep: 50 nA<br>• Avg @ 1 Hz: 550 nA (RH+T, 11-bit) | $1.58 – $1.58 | 1000+ ([Mouser](https://www.mouser.com/ProductDetail/Texas-Instruments/HDC2010YPAR?qs=EU6FO9ffTwc2CasHMLP4bA%3D%3D) & [Digi-Key](https://www.digikey.com/en/products/detail/texas-instruments/HDC2010YPAR/7561364)) |


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
| [ENS160](https://www.sciosense.com/wp-content/uploads/2023/12/ENS160-Datasheet.pdf) | VOC / IAQ | • On-sensor IAQ outputs (TVOC + eCO2 eq)<br>• Simple host integration | • Warm-up/conditioning overhead after idle/power-off<br>• High risk for coin-cell use at ≥1/hr updates | • Std: ~29 mA avg (Std)<br>• Warm-up: ~3 min typical after idle/power-off | $5.63 – $5.63 | 1000+ ([Mouser](https://www.mouser.com/c/sensors/environmental-sensors/?series=ENS160) & [Digi-Key](https://www.digikey.com/en/products/detail/sciosense/ENS160-BGLT/16129831)) |
| [BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | Multi-sensor | • IAQ + T/H + Pressure in one<br>• Low average-current profiles available | • IAQ/eCO2 typically assumes BSEC integration<br>• Heater power tradeoffs | • ULP IAQ: ~0.09 mA avg<br>• LP IAQ: ~0.9 mA avg<br>• Std gas scan: ~3.9 mA avg | $8.65 – $8.65 | [Digi-Key](https://www.digikey.com/en/products/detail/bosch-sensortec/BME688/13681261): 10,000+<br>[Mouser](https://www.mouser.com/ProductDetail/Bosch-Sensortec/BME688?qs=IS%252B4QmGtzzqQoVDscqwx3A%3D%3D): <1000 (6,000 exp 3/3/2026) |

### Notes & Considerations
- Both options are heater-based; duty-cycling and sampling cadence strongly influence battery life.
- ENS160 provides processed IAQ outputs on-sensor but has warm-up/conditioning overhead after idle/power-off that can dominate energy-per-reading.
- BME688 supports low average-current profiles; IAQ and eCO2-style outputs are typically obtained via Bosch BSEC.

### Decision Rationale (Current Baseline)
- Rev A (coin cell) shall use **BME688** as the baseline VOC/IAQ sensor to support ≥1/hr updates with manageable average power.
- ENS160 is not recommended for Rev A due to warm-up/conditioning overhead; it may remain an optional Rev B / USB-powered candidate if on-sensor processing is preferred.
- “eCO2” is an equivalent output derived from VOC sensing/algorithms and is not a direct CO2 ppm measurement. Any future requirement for true CO2 shall use a dedicated CO2 sensor (e.g., NDIR).

**Preliminary Direction:** BME688 (Rev A baseline); ENS160 optional for Rev B / USB-mode only  
**Decision Status:** Frozen for Rev A; TBD for Rev B



---

### 3.3 Ambient Light (Lux) (Required)

#### Candidate Comparison

| Candidate | Interface | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| [Vishay VEML7700](https://www.vishay.com/docs/84286/veml7700.pdf) | I²C | • Lux ALS<br>• Very low power modes<br>• Strong distributor stock | • Not multispectral | • Shutdown: 0.5 µA typ<br>• Active: ~45 µA typ | $1.09 – $1.24 | 1000+ ([Mouser](https://www.mouser.com/ProductDetail/Vishay-Semiconductors/VEML7700-TT?qs=BcfjnG7NVaXdL6DJFdWbdw%3D%3D) & [Digi-Key](https://www.digikey.com/en/products/detail/vishay-semiconductor-opto-division/VEML7700-TT/6210690)) |
| [ams OSRAM AS7341](https://look.ams-osram.com/m/24266a3e584de4db/original/AS7341-DS000504.pdf) | I²C | • Multispectral (VIS + NIR + clear)<br>• Enables color/spectral features | • NRND lifecycle risk<br>• Higher cost / power vs lux-only | • Active: <300 µA (spec)<br>• Sleep: <5 µA (spec) | $6.90 – $7.64 | [Mouser](https://www.mouser.com/ProductDetail/ams-OSRAM/AS7341-DLGM?qs=byeeYqUIh0OzxJ%252B6BPJ%252BEQ%3D%3D): 488<br>[Digi-Key](https://www.digikey.com/en/products/detail/ams-osram-usa-inc/AS7341-DLGM/9996230): 3,523 |
| [ams OSRAM TCS34488M-OLGA8 (TCS3448 family)](https://look.ams-osram.com/m/1c24b057e65ee61e/original/TCS3448-14-Channel-multi-spectral-sensor.pdf) | I²C | • 14-channel spectral (VIS + NIR + clear + flicker)<br>• Intended replacement direction for AS734x-class parts | • Vendor portfolio complexity (family/ordering variants)<br>• Digi-Key flags some variants as LTB (verify lifecycle) | • Active: 210–280 µA typ<br>• Idle: 40–60 µA typ<br>• Sleep: 0.7–5 µA typ | $6.12 – $6.79 | [Mouser](https://www.mouser.com/ProductDetail/ams-OSRAM/TCS34488M-OLGA8?qs=sqEgtWRSLJ16dB5JzLAEyQ%3D%3D): 1,812<br>[Digi-Key](https://www.digikey.com/en/products/detail/ams-osram-usa-inc/TCS34488M-OLGA8-LF-T-RDP/26705585): 380 (LTB: 3/31/2027) |

### Notes & Considerations
- Ambient light sensing is required for both revisions. The baseline implementation shall use a simple lux ALS (VEML7700).
- A multispectral sensor is an optional enhancement for Rev B only (AS7341 / TCS34488M), intended to enable future color/spectral features.
- The ams OSRAM multispectral portfolio includes multiple closely related families and ordering variants; final selection shall be based on confirmed lifecycle status and distributor support.
  - Lifecycle/availability risk remains; final selection shall be revalidated prior to any larger build.
  - Acceptable for prototypes and small runs.


**Preliminary Direction:** VEML7700 (Rev A + Rev B baseline); multispectral optional for Rev B (TBD)  
**Decision Status:** Pending schematic validation and power budget study



---

### 3.4 Optional: Barometric Pressure

#### Candidate Comparison

| Candidate | Interface | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| [BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) (if selected for VOC) | I²C / SPI | • Zero additional BOM<br>• Already integrated if VOC/IAQ uses BME688 | • Pressure availability depends on VOC/IAQ decision | • Pressure+Temp @ 1 Hz (forced, low power): 3.1 µA typ | $0 (incremental) | See VOC/IAQ section |
| [BMP581](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bmp581-ds004.pdf) | I²C / SPI | • Low BOM impact<br>• Strong distributor stock | • Adds another IC (BOM + assembly + board area) | • Avg @ 1 Hz: ~1.3 µA typ<br>• Deep standby: ~0.5 µA typ | $2.92 – $2.98 | 11,784 ([Mouser](https://www.mouser.com/ProductDetail/Bosch-Sensortec/BMP581?qs=Li%252BoUPsLEntPL9tlFmcgXg%3D%3D))<br>21,697 ([Digi-Key](https://www.digikey.com/en/products/detail/bosch-sensortec/BMP581/16036134)) |
| [ENS220](https://www.sciosense.com/wp-content/uploads/2025/10/ENS220-Datasheet-1.pdf) | I²C / SPI | • Low BOM impact<br>• Ultra-low average current modes | • Adds another IC (BOM + assembly + board area) | • Example: 1.7 µA @ 1 Hz (pulsed)<br>• Example: 0.1 µA @ 1/60 Hz (pulsed) | $2.57 – $2.92 | 789 ([Mouser](https://www.mouser.com/ProductDetail/ScioSense/ENS220S-BLGT-LGA10-TR-3K5?qs=17ckDYBRdelo27HqwbDxBQ%3D%3D))<br>2,791 ([Digi-Key](https://www.digikey.com/en/products/detail/sciosense/ENS220S-BLGT/21278457)) |

### Notes & Considerations
- If **BME688 is selected for VOC/IAQ**, pressure is considered **implicitly satisfied** at **no incremental BOM cost**.
- If VOC/IAQ uses a non-pressure part (e.g., ENS160), the preferred low-cost pressure add-on candidates are **BMP581** and **ENS220**.
- Any required 1.8 V domain (including for other components) is assumed to be addressed by the system power architecture and is not treated as a disqualifying factor for ENS220.

**Preliminary Direction:** BME688 pressure if selected for VOC/IAQ; otherwise BMP581 or ENS220  
**Decision Status:** Pending schematic validation and power budget study




---

### 3.5 Optional: Sound Level / dB

A dedicated “dB sensor” (direct calibrated dBA output) is not commonly available as a low-cost IC. The practical implementation is a microphone (preferably digital) plus firmware to compute sound level (RMS, optional A-weighting). This block may be omitted if the power/firmware cost is not justified.

#### Candidate Comparison

| Candidate | Sensor Type | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|-------------|----------|----------|--------------|-----------------|--------------|
| [Infineon IM69D130V01XTSA1](https://www.infineon.com/assets/row/public/documents/24/49/infineon-im69d130-datasheet-en.pdf) | Digital MEMS mic (PDM), bottom-port | • Active part (not LTB/EOL)<br>• Strong ecosystem/stock | • Not a “dB sensor” (DSP + calibration needed)<br>• High active current for coin-cell unless duty-cycled | • Icc: ~1.3 mA typ | $1.91 – $2.02 | [Mouser](https://www.mouser.com/ProductDetail/Infineon-Technologies/IM69D130V01XTSA1?qs=W0yvOO0ixfHjr98Yrg6FIA%3D%3D): 6,800+<br>[Digi-Key](https://www.digikey.com/en/products/detail/infineon-technologies/IM69D130V01XTSA1/8030732): 5,000+ |
| [Syntiant SPH0641LU4H-1](https://www.mouser.com/datasheet/2/218/-746191.pdf) | Digital MEMS mic (PDM), bottom-port | • Low-power capable modes<br>• Strong stock depth | • Not a “dB sensor” (DSP + calibration needed)<br>• High active current for coin-cell unless duty-cycled | • Icc: ~1.0 mA typ | $3.22 – $3.22 | [Mouser](https://www.mouser.com/ProductDetail/Syntiant/SPH0641LU4H-1-8?qs=zEmsApcVOkWTK1aVriKK3w%3D%3D): 5,700+<br>[Digi-Key](https://www.digikey.com/en/products/detail/syntiant/SPH0641LU4H-1/5332438): 18,000+ |
| [ST MP34DT06JTR](https://www.st.com/resource/en/datasheet/mp34dt06j.pdf) | Digital MEMS mic (PDM), port varies by package | • Common PDM mic option<br>• Good stock (today) | • Distributor status indicates lifecycle risk (LTB / discontinuation flags) | • Icc: ~650 µA typ | $2.17 – $2.44 | [Mouser](https://www.mouser.com/ProductDetail/STMicroelectronics/MP34DT06JTR?qs=%252BEew9%252B0nqrDi3H2kv8Y1Xg%3D%3D): 5,800+ (EOL flagged)<br>[Digi-Key](https://www.digikey.com/en/products/detail/stmicroelectronics/MP34DT06JTR/9605993): 7,000+ (LTB) |

#### Notes & Considerations

- **Power risk (Rev A coin cell):** all listed microphones draw on the order of **~0.65–1.3 mA when enabled**, which is too high for continuous operation. If implemented on Rev A, this block must be aggressively duty-cycled (short sampling windows at low cadence) or omitted.
- **Output expectation:** without calibration, results should be treated as **relative sound level** (quiet vs loud). Achieving meaningful absolute dBA accuracy requires calibration and enclosure/acoustic considerations.
- **Port location / PCB orientation:**
  - **Bottom-port** microphones couple sound through an opening beneath the package (via a PCB acoustic via/hole to the outside world). If mounted on the top side of the PCB, a bottom-port mic can still sense sound from the “top” of the product, but only if the PCB has a properly designed acoustic port path to that side (hole + keepout + gasket/mesh as needed).
  - Without a designed acoustic port, the mic will be heavily attenuated and sensitive to internal cavity effects; it will not reliably “listen through” solid PCB/enclosure material.
  - **Top-port** microphones simplify enclosure integration when the opening is on the same side as the component.

**Preliminary Direction:** TBD (optional; omit unless a clear use-case is defined)  
**Decision Status:** TBD – pending power budget study and enclosure/acoustic constraints



---

## 4) Power Management Strategy

This section defines the power architecture requirements and the PMIC/DC-DC candidate selection approach. Because Rev A and Rev B have fundamentally different power sources and load profiles, power management is tracked seperately.

---

### 4.1 Power Architecture Requirements (Both Revisions)

#### Voltage Domains (Planned)
- **VDD_MAIN (3.3 V):** primary system rail for MCU + sensors
- **VDD_1V8 (1.8 V):** secondary rail for components requiring 1.8 V operation
- **VDD_WIFI (Rev B only, optional / TBD):** dedicated rail for Wi-Fi module if required to meet sleep/leakage or power gating requirements

#### Power Path / Source Selection (Planned)
- **Rev A:** regulated coin cell supply (regulator topology TBD)
- **Rev B:** power-path PMIC providing:
  - USB-powered operation when available
  - LiPo battery charging from USB
  - Seamless transition between USB power and battery without brownouts (USB → battery and battery → USB)

#### Power Management Responsibilities (Planned)
- Firmware shall select operating policy based on power source state:
  - USB present: less aggressive duty cycling; Wi-Fi allowed/available
  - Battery: aggressive duty cycling; Wi-Fi duty-cycled and/or disabled per policy
- Power path behavior (source switchover, charge control) shall be handled by hardware (PMIC), not firmware.

#### Power Gating / Switching (Planned)
- Provide a mechanism to **disable the Wi-Fi block** (Rev B), pending confirmation that Wi-Fi sleep/leakage current is acceptable without gating.
- Sensor power gating is deferred; sensors shall rely on their sleep modes for initial designs.

#### Measurement / Monitoring (Planned)
- **Rev A:** battery voltage measurement via ADC divider (implementation TBD)
- **Rev B:** battery measurement approach TBD (ADC divider vs more complex solution)
- **Rev B:** charging / power-source status signals are required (exact signals TBD by PMIC choice)

#### Rail Map
- Rail-to-load mapping is **TBD** and will be completed during schematic capture.

**Decision Status:** Pending schematic validation and power budget study

---

### 4.2 Rev A – Coin Cell Power Path (BLE-only)

#### Candidate Comparison (Rev A Regulators / Power Path Options)

| Candidate | Topology | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| Direct coin cell → VDD_MAIN | None | • Minimal BOM<br>• Lowest quiescent current | • Voltage droop under peaks<br>• Brownout risk depends on load profile | Best for sleep; peak-limited | $ | TBD |
| Buck/boost regulator (TBD) | DC/DC | • Stable rail across coin cell discharge<br>• Better peak handling | • Added quiescent current<br>• Added BOM + layout complexity | TBD | $$ | TBD |
| LDO regulator (TBD) | LDO | • Simple, low noise | • Efficiency loss at higher load<br>• Still peak-limited by cell | TBD | $$ | TBD |

#### Notes & Considerations (Rev A)
- Rev A target is minimum sleep current; regulator quiescent current is a primary selection driver.
- Final decision depends on measured peak load behavior (BLE TX bursts, sensors, any optional blocks).

**Preliminary Direction (Rev A):** Direct coin cell unless power budget study shows unacceptable droop/brownout risk  
**Decision Status (Rev A):** TBD – pending power budget study

---

### 4.3 Rev B – LiPo + USB-C Power Path (BLE + Wi-Fi)

#### Candidate Comparison (Rev B PMIC / Charger / Power Path Options)

| Candidate | Function | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|----------|----------|----------|--------------|-----------------|--------------|
| USB-C + LiPo charger + system power-path PMIC (TBD) | Charger + power mux | • Seamless USB/LiPo operation<br>• Supports “always-on” USB mode | • More complex<br>• Part selection critical for Wi-Fi transients | Supports Wi-Fi peaks if designed correctly | $$ | TBD |
| Charger + separate buck regulator (TBD) | Charger + DC/DC | • Modular; easier sourcing<br>• Can optimize DC/DC for Wi-Fi load | • More components<br>• More layout effort | TBD | $$ | TBD |
| Fuel gauge (optional, TBD) | Measurement | • Better battery reporting than ADC divider | • Added cost/IC | Low | $–$$ | TBD |

#### Notes & Considerations (Rev B)
- Rev B must support **Wi-Fi peak current (~260 mA TX)** and fast transient response without brownout.
- Rev B must support both modes:
  - **USB-powered:** Wi-Fi enabled, always reachable
  - **Battery-powered:** Wi-Fi gated/duty-cycled aggressively (Wi-Fi module may be DNP)
- PMIC selection must be compatible with the chosen battery size/chemistry and desired charge current.

**Preliminary Direction (Rev B):** Power-path PMIC or charger + DC/DC (TBD)  
**Decision Status (Rev B):** TBD – pending PMIC selection and power budget study


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
