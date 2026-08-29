# MatterSense — Hardware Block Selection / Trade Study

**Purpose:**
This document captures major hardware block options for MatterSense Rev A and Rev B prior to schematic and PCB design. It evaluates candidate technologies and ICs at a block level and records the selected direction and remaining decision status.

This document complements the HRS and will evolve as the remaining schematic and validation selections are completed.

Distributor pricing and inventory in candidate tables are point-in-time trade-study
inputs, not design requirements. The selected baseline table near the end of this
document identifies the authoritative orderable variants; stock shall be rechecked
before every prototype or production buy.

---

## 1) MCU / SoC Candidates

### Candidate Comparison

| Candidate | Form Factor | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
| --- | --- | --- | --- | --- | --- | --- |
| [nRF52840 (Discrete)](https://docs-be.nordicsemi.com/bundle/ps_nrf52840/attach/nRF52840_PS_v1.11.pdf?_LANG=enus) | SoC | • Lowest BOM cost<br>• Full control over RF/layout<br>• Maximum flexibility | • Requires RF design effort<br>• FCC/IC certification burden<br>• Higher validation and schedule risk | • TX: 4.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 3.16 µA | $3.59 – $5.48 | 1000+ <br>([Mouser](https://www.mouser.com/c/semiconductors/wireless-rf-integrated-circuits/rf-system-on-a-chip-soc/?m=Nordic%20Semiconductor&series=nRF52840) & [Digi-Key](https://www.digikey.com/en/products/detail/nordic-semiconductor-asa/NRF52840-QIAA-R7/11200025)) |
| [Raytac MDBT50Q-1M](https://www.mouser.com/datasheet/2/744/_5bnRF52840_5d_MDBT50Q_1MV2__26_MDBT50Q_P1MV2_Ver_-2525658.pdf?srsltid=AfmBOorB5Z35m8gI5794esgzA8Nj53-aUOLUO8bO6VV-565TyayQimsI) | Module | • Historically low-cost certified module<br>• Compact footprint | • Currently unavailable<br>• Potential lifecycle/EOL risk | Not evaluated because the candidate was rejected on availability | $6.00 – $9.00 | 0 <br>([Mouser](https://www.mouser.com/c/?q=MDBT50Q) & [Digi-Key](https://www.digikey.com/en/products/detail/raytac/MDBT50Q-1MV2/13677591)) |
| [u-blox BMD-340-A-R](https://content.u-blox.com/sites/default/files/BMD-340_DataSheet_UBX-19033353.pdf) | Module | • Fully certified (FCC/IC/CE)<br>• Strong vendor documentation<br>• Lower cost than BL654 | • Less field history than BL654 | • TX: 4.8 mA – 14.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 2.35 µA | $7.64 – $9.60 | 1000+ <br>([Mouser](https://www.mouser.com/ProductDetail/u-blox/BMD-340-A-R?qs=wd5RIQLrsJgGcI5R13BUYg%3D%3D&srsltid=AfmBOorkcgR2_ZodEhfTeudAxMhkFS4pwKuMG93ztHSLkbOvfiFKmboP) & [Digi-Key](https://www.digikey.com/en/products/detail/u-blox/BMD-340-A-R/8638939)) |
| [Ezurio BL654 451-00001](https://www.ezurio.com/documentation/datasheet-bl654) | Module with integrated trace antenna | • Pre-certified (FCC/IC/CE)<br>• Proven production module<br>• Strong RF performance<br>• Simplifies RF and schedule risk | • Higher BOM vs discrete | • TX: 4.8 mA – 14.8 mA<br>• RX: 4.6 mA<br>• Sleep: 0.4 µA – 3.1 µA | $11.63 – $12.08 | Active and stocked by [Mouser](https://www.mouser.com/ProductDetail/Ezurio/451-00001?qs=MLItCLRbWszU6GB3nrHvkA%3D%3D&srsltid=AfmBOorA1ltk7Dfq18QRUn1tKwWPF2PeBid5a2opnczd6jA04DZWorvP) and [Digi-Key](https://www.digikey.com/en/products/detail/ezurio/451-00001/9172334) |
| [Fanstel WT02C40C](https://fanstel.squarespace.com/s/WT02C40C-Product-Specifications-h97f.pdf) | nRF5340+nRF7002 combo module with two chip antennas | • Nordic's supported Matter-over-Wi-Fi host/companion class<br>• BLE, 802.15.4, dual-band Wi-Fi, and NFC<br>• Integrated RF, crystals, DC/DC passives, coexistence wiring, and Wi-Fi power switch<br>• Lower module cost than separate BL654+WM02C | • Newer, less broadly stocked part<br>• Large dual-antenna keepout<br>• The nRF5340's sole QSPI controller and QSPI CSN net serve nRF7002; the shared QSPI signal nets remain exposed at module pads<br>• LGA pads required for full GPIO access | • Wi-Fi-on peak: approximately 270 mA for the complete module<br>• nRF7002 can be hard-off through the internal switch | $13.99 direct; distributor pricing varies | Active; 183 at [Digi-Key](https://www.digikey.com/en/products/detail/fanstel-corp/WT02C40C/26639091) and available direct in the 2026-08-15 check |
| [ESP32-WROOM-32 (Reference Only)](https://www.espressif.com/sites/default/files/documentation/esp32-wroom-32_datasheet_en.pdf) | Module | • Very low cost<br>• Integrated Wi-Fi + BLE<br>• Massive ecosystem | • High idle power<br>• Poor fit for coin-cell operation<br>• Firmware and power model divergence | • TX: 130 mA – 240 mA<br>• RX: 9 mA – 100 mA<br>• Sleep: 10 µA – 0.8 mA | $3.50 – $5.00 | 1000+ <br>([Mouser](https://www.mouser.com/ProductDetail/Espressif-Systems/ESP32-WROOM-32E-N4?qs=Li%252BoUPsLEnsPzTWsi%252BRMgQ%3D%3D&srsltid=AfmBOooi9ADzgFX94NQYgr2DWTCfaqRWz4_b8t2TaKm5tqPpJovUdi3F) & [Digi-Key](https://www.digikey.com/en/products/detail/espressif-systems/ESP32-WROOM-32-N4/8544298)) |


### Notes & Considerations

- **Ezurio BL654 451-00001 is the selected Rev A baseline** due to:
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

- NFC pin exposure suitable for NFC-assisted Matter onboarding
- Internal flash/RAM sufficient for the active Matter image and runtime; external serial NOR is required for robust OTA staging and data storage
- Required peripheral exposure confirmed (GPIO, ADC, I²C, SPI, QSPI, IEEE 802.15.4, and NFC)

**Selected Baseline (Rev A):** Ezurio BL654 451-00001
**Selected Baseline (Rev B):** Fanstel WT02C40C nRF5340+nRF7002 combo module
**Decision Status:** Both revision baselines frozen for schematic capture

Both baselines use protected internal SoC storage, secure boot, controlled factory
provisioning, and production debug-port protection for device identity and private
keys. An external secure element is not included in the baseline BOM.

### nRF52840 + nRF7002 Compatibility and Rev B Decision

The nRF52840 is capable of operating an nRF7002. [Nordic describes nRF7002](https://www.nordicsemi.com/Products/nRF7002/Modules) as a
companion for nRF52 and nRF53 hosts, the [nRF7002 EK](https://docs.nordicsemi.com/bundle/ncs-2.9.3/page/zephyr/boards/shields/nrf7002ek/doc/index.html) uses an SPI-family host interface,
and Nordic's current nRF52840 Wi-Fi station memory table reports a working station
build using approximately [565 KB ROM and 179 KB RAM](https://docs.nordicsemi.com/bundle/ncs-3.2.4/page/nrf/protocols/wifi/station_mode/mem_requirements_sta.html).

The remaining distinction is supported product scope. [Nordic's Matter
sample/certification references](https://docs.nordicsemi.com/bundle/ncs-3.0.0/page/nrf/protocols/matter/end_product/ecosystems_certification.html) list nRF52840 for Matter-over-Thread and the nRF7002
DK, which uses nRF5340 as host, for Matter-over-Wi-Fi. A complete nRF52840 product
image would also need to fit Matter, Wi-Fi, BLE commissioning, BLE Local Mode,
BSEC/sensors, logging, and OTA into the remaining memory. The separate BL654+WM02C
architecture is technically possible but carries higher firmware-integration risk.

Rev B instead selects the Fanstel WT02C40C. It combines nRF5340 and nRF7002 with
two integrated chip antennas, follows the nRF7002 DK interconnect, exposes NFC/SWD/
ADC/I²C and the required GPIOs, embeds the Wi-Fi power switch and crystals, and costs
less than the approximately $20.72–$21.73 one-piece total for separate BL654 and
WM02C modules before the external load switch and assembly cost. The
[nRF5340](https://docs.nordicsemi.com/r/bundle/ps_nrf5340/page/chapters/memory/appmem.html)
and [nRF52840](https://docs.nordicsemi.com/r/bundle/ps_nrf52840/page/memory.html)
each provide one hardware QSPI controller, along with several SPI/SPIM instances.
In WT02C40C, the nRF5340 QSPI clock, data, and chip-select nets connect
internally to nRF7002; Fanstel also exposes those shared nets at module pads. They do
not form a second independent QSPI bus, and the sole dedicated QSPI CSN net is already
connected to nRF7002. Rev B therefore connects external flash to an independent
SPIM4 instance in standard SPI mode rather than adding bus-selection
hardware and non-reference arbitration firmware.

**Decision Status (Rev B host/Wi-Fi):** WT02C40C frozen for schematic baseline; pin mapping, RF keepout, power, and firmware remain schematic/validation work

---


## 2) Wi-Fi and Combo-Radio Candidates (Rev B)

### Candidate Comparison

| Candidate | Form Factor | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
| --- | --- | --- | --- | --- | --- | --- |
| [nRF7002 (Discrete)](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/6470/NRF7002-QFAA-R.pdf) | IC | • Native Nordic ecosystem<br>• Designed as Wi-Fi companion to nRF52<br>• Avoids dependence on module stock | • Requires RF design and antenna tuning<br>• Separate FCC/IC certification effort | • TX: up to ~260 mA (5 GHz)<br>• RX: ~56–60 mA<br>• Sleep: ~15 µA<br>• Shutdown: ~1.7 µA | $2.58 | 1000+ ([Digi-Key](https://www.digikey.com/en/products/detail/nordic-semiconductor-asa/NRF7002-QFAA-R/17738488)) |
| [MinewSemi MS14SF1-1N02AIR](https://en.minewsemi.com/file/MS14SF1-nRF7002_Datasheet_K_EN.pdf) | Module (PCB Antenna) | • Integrated PCB antenna<br>• Sold through Digi-Key (Marketplace)<br>• Good candidate if stock remains available | • Marketplace sourcing (not primary franchised line)<br>• Less established ecosystem vs Fanstel | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $10.00 | 1000+ ([Digi-Key](https://www.digikey.com/en/products/detail/minewsemi/MS14SF1-1N02AIR/26409766)) |
| [Fanstel WM02F](https://static1.squarespace.com/static/561459a2e4b0b39f5cefa12e/t/672e447ee28d49742366de31/1731085441199/WM02C%2BProduct%2BSpecifications.pdf) | Module (PCB Trace Antenna) | • Integrated PCB trace antenna<br>• Attractive cost when sourced direct<br>• Module-first path for Rev B | • Not stocked at Mouser/Digi-Key (10+ week LT; MOQ 1000)<br>• Reliance on vendor-direct inventory | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $4.20 – $5.03 ([Fanstel direct](https://www.fanstel.com/buy/bt840f-v1-nrf52840-bluetooth-5-thread-zigbee-module-wah32-amhmr-6meyb-hlhel)) | ~600 ([Fanstel direct](https://www.fanstel.com/buy/bt840f-v1-nrf52840-bluetooth-5-thread-zigbee-module-wah32-amhmr-6meyb-hlhel))<br>0 ([Mouser](https://www.mouser.com/ProductDetail/Fanstel/WM02F?qs=ST9lo4GX8V39vk8QtDIbrQ%3D%3D) & [Digi-Key](https://www.digikey.com/en/products/detail/fanstel-corp/WM02F/22107923)) |
| [Fanstel WM02C](https://fanstel.squarespace.com/s/WM02C-Product-Specifications-3py2.pdf) | Module (Chip Antenna) | • Integrated chip antenna<br>• Stocked at major distributors<br>• Good near-term buyable option | • Smaller sourcing ecosystem than commodity Wi-Fi modules<br>• Antenna performance/layout keepouts still matter | • TX: up to ~260 mA<br>• RX: ~56–60 mA<br>• Idle (TWT): ~18–30 µA (chip capability) | $9.09 – $9.65 | Active; 1,144 at [Digi-Key](https://www.digikey.com/en/products/detail/fanstel-corp/WM02C/22107887) and 321 at [Mouser](https://www.mouser.com/ProductDetail/Fanstel/WM02C?qs=ST9lo4GX8V2bfCVo1J5PEw%3D%3D) in the 2026-08-12 sourcing check |
| [Fanstel WT02C40C (Selected Combo)](https://fanstel.squarespace.com/s/WT02C40C-Product-Specifications-h97f.pdf) | nRF5340+nRF7002 module, two chip antennas | • One module for BLE/Thread/Wi-Fi<br>• Integrated antennas, coexistence, clocks, DC/DC passives, and Wi-Fi switch<br>• Reference-like nRF7002 DK interconnect<br>• Lower combined module/BOM cost | • Large RF keepout<br>• Sole nRF5340 QSPI controller and QSPI CSN net serve nRF7002; the shared signal nets remain exposed at module pads<br>• Newer/shallower distribution stock | • Complete-module peak approximately 270 mA with Wi-Fi power save off<br>• nRF7002 hard-off supported internally | $13.99 direct; distributor pricing varies | Active; 183 at [Digi-Key](https://www.digikey.com/en/products/detail/fanstel-corp/WT02C40C/26639091) and direct availability in the 2026-08-15 check |
| [Fanstel WT02E40E (Combo Alternate)](https://www.mouser.com/datasheet/2/915/WT02E40E_2bProduct_2bSpecifications-3576516.pdf) | nRF5340+nRF7002 module, two u.FL connectors | • Same integrated digital architecture as WT02C40C<br>• Flexible external-antenna placement | • Two antennas/cables add BOM and enclosure work<br>• Does not meet the preferred integrated-antenna baseline | • Same nRF5340+nRF7002 class | $19.31 at quantity 1 from Mouser; $13.03 direct | Active; stocked at Mouser and available direct in the 2026-08-15 check |


### Notes & Considerations

- Rev B assumes **Wi-Fi is optional and power-managed according to the active build**:
  - Wi-Fi may be **power-gated during battery operation** in the Matter-over-Thread build or while Wi-Fi is intentionally unavailable
  - A Matter-over-Wi-Fi build must instead retain association using a supported Wi-Fi power-save mode; the scheduled hard-off/upload model is not a Matter reachability policy
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
- **Thread-only population strategy:**
  - The WT02C40C footprint is compatible with Fanstel's BT40F nRF5340-only module. A Rev B Thread-only population may fit BT40F instead of WT02C40C and omit the pin-17 Wi-Fi supply components.
  - The Thread-only build shall retain BLE commissioning and BLE Local Mode and shall use the corresponding board/firmware configuration.
  - The host module itself cannot be DNP because it contains the primary MCU. The pin-compatible population option preserves one PCB while reducing BOM cost for builds where Wi-Fi is not required.

### Firmware Policy Items (Wi-Fi Block)

- Define firmware policy for:
  - Battery vs USB Wi-Fi behavior
  - TWT configuration and wake cadence

### Closed Items (Wi-Fi Block)

- Integrated antenna requirement captured (modules requiring external antennas excluded from primary shortlist)
- WT02C40C internal nRF5340-to-nRF7002 interface accepted; it matches the nRF7002 DK QSPI/coexistence arrangement and exposes the same shared QSPI nets at module pads.
- Rev B external NOR uses the independent SPIM4 peripheral in standard SPI mode; Rev A retains QSPI flash.
- TPS63802 power path validated at the architecture level for the approximately 300 mA system peak; the WT02C40C internal Wi-Fi switch replaces the external TPS22919. Schematic transient/layout validation remains.
- Thread-only population strategy captured:
  - Fit footprint-compatible BT40F instead of WT02C40C and load the Thread firmware configuration; runtime transport switching is not required
- Wi-Fi peak current / power-state constraints captured (approximately 270 mA complete-module peak; sleep/shutdown and TWT targets)
- Primary Rev B radio/module selected: **Fanstel WT02C40C**
  - Rationale: supported nRF5340+nRF7002 architecture, two integrated chip antennas, lower combined BOM/module cost, and sufficient project-scale availability

**Selected Baseline:** Fanstel WT02C40C (nRF5340+nRF7002 combo module with two integrated chip antennas)
**Decision Status:** Frozen for Rev B schematic baseline; final pin allocation, RF keepout, firmware configuration, and measured power validation pending


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

**Selected Baseline:** SHTC3-TR-2.5KS
**Decision Status:** Frozen for both revisions

---

### 3.2 VOC / IAQ (Required)

#### Candidate Comparison

| Candidate | Sensor Class | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|--------------|----------|----------|--------------|-----------------|--------------|
| [ENS160](https://www.sciosense.com/wp-content/uploads/2023/12/ENS160-Datasheet.pdf) | VOC / IAQ | • On-sensor IAQ outputs (TVOC + eCO2 eq)<br>• Simple host integration | • Warm-up/conditioning overhead after idle/power-off<br>• High risk for coin-cell use at ≥1/hr updates | • Std: ~29 mA avg (Std)<br>• Warm-up: ~3 min typical after idle/power-off | $5.63 – $5.63 | 1000+ ([Mouser](https://www.mouser.com/c/sensors/environmental-sensors/?series=ENS160) & [Digi-Key](https://www.digikey.com/en/products/detail/sciosense/ENS160-BGLT/16129831)) |
| [BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | Multi-sensor | • IAQ + T/H + Pressure in one<br>• Low average-current profiles available | • IAQ/eCO2 typically assumes BSEC integration<br>• Heater power tradeoffs | • ULP IAQ: ~0.09 mA avg<br>• LP IAQ: ~0.9 mA avg<br>• Std gas scan: ~3.9 mA avg | $8.65 – $8.65 | [Digi-Key](https://www.digikey.com/en/products/detail/bosch-sensortec/BME688/13681261)<br>[Mouser](https://www.mouser.com/ProductDetail/Bosch-Sensortec/BME688?qs=IS%252B4QmGtzzqQoVDscqwx3A%3D%3D)<br>See the selected-baseline table for the dated stock snapshot |

### Notes & Considerations
- Both options are heater-based; duty-cycling and sampling cadence strongly influence battery life.
- ENS160 provides processed IAQ outputs on-sensor but has warm-up/conditioning overhead after idle/power-off that can dominate energy-per-reading.
- BME688 supports low average-current profiles; IAQ and eCO2-style outputs are typically obtained via Bosch BSEC.
- Bosch states that the device is optimized for 1.8 V, and the published 90 µA BSEC ULP average is specified at VDD ≤ 1.8 V. Equivalent gas-mode current at 3.0 V or 3.3 V is not characterized in the datasheet.

### Decision Rationale (Current Baseline)
- Rev A (coin cell) shall use **BME688** as the baseline VOC/IAQ sensor to support ≥1/hr updates with manageable average power.
- ENS160 is not recommended for Rev A due to warm-up/conditioning overhead; it may remain an optional Rev B / USB-powered candidate if on-sensor processing is preferred.
- “eCO2” is an equivalent output derived from VOC sensing/algorithms and is not a direct CO2 ppm measurement. Any future requirement for true CO2 shall use a dedicated CO2 sensor (e.g., NDIR).
- Pre-v1.0 evaluation boards shall populate the TPS62840 1.8 V buck and one break-before-make micro DIP/slide selector with an SPDT source-selection function so BME688 VDD can be switched between the main rail and 1.8 V without soldering. VDDIO remains on the main rail. The switch shall be changed only while unpowered. Production population will be selected after battery-input energy is measured with identical BSEC ULP profiles.

**Selected Baseline:** BME688 (Rev A + Rev B baseline); ENS160 optional for a future Rev B / USB-only variant
**Decision Status:** Sensor selection frozen; BME688 supply population pending prototype A/B measurement



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


**Selected Baseline:** VEML7700 (Rev A + Rev B baseline); multispectral optional for a future Rev B variant
**Decision Status:** Frozen for both baseline revisions



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

**Selected Baseline:** Use pressure data from the baseline BME688; no separate pressure sensor
**Decision Status:** Frozen for both baseline revisions




---

### 3.5 Optional: Sound Level / dB

A dedicated “dB sensor” (direct calibrated dBA output) is not commonly available as a low-cost IC. The practical implementation is a microphone (preferably digital) plus firmware to compute sound level (RMS, optional A-weighting). This block may be omitted if the power/firmware cost is not justified.

#### Candidate Comparison

| Candidate | Sensor Type | Key Pros | Key Cons | Power Impact | Cost (Ballpark) | Availability |
|---------|-------------|----------|----------|--------------|-----------------|--------------|
| [Infineon IM69D130V01XTSA1](https://www.infineon.com/assets/row/public/documents/24/49/infineon-im69d130-datasheet-en.pdf) | Digital MEMS mic (PDM), bottom-port | • Active part (not LTB/EOL)<br>• Strong ecosystem/stock | • Not a “dB sensor” (DSP + calibration needed)<br>• High active current for coin-cell unless duty-cycled | • Icc: ~1.3 mA typ | $1.91 – $2.02 | [Mouser](https://www.mouser.com/ProductDetail/Infineon-Technologies/IM69D130V01XTSA1?qs=W0yvOO0ixfHjr98Yrg6FIA%3D%3D): 6,800+<br>[Digi-Key](https://www.digikey.com/en/products/detail/infineon-technologies/IM69D130V01XTSA1/8030732): 5,000+ |
| [Syntiant SPH0641LU4H-1](https://www.mouser.com/datasheet/2/218/-746191.pdf) | Digital MEMS mic (PDM), bottom-port | • Low-power capable modes<br>• Strong stock depth | • Not a “dB sensor” (DSP + calibration needed)<br>• High active current for coin-cell unless duty-cycled | • Icc: ~1.0 mA typ | $3.22 – $3.22 | [Mouser](https://www.mouser.com/ProductDetail/Syntiant/SPH0641LU4H-1-8?qs=zEmsApcVOkWTK1aVriKK3w%3D%3D): 5,700+<br>[Digi-Key](https://www.digikey.com/en/products/detail/syntiant/SPH0641LU4H-1/5332438): 18,000+ |
| [ST MP34DT06JTR](https://www.st.com/resource/en/datasheet/mp34dt06j.pdf) | Digital MEMS mic (PDM), port varies by package | • Common PDM mic option | • Distributor status indicates lifecycle risk (LTB / discontinuation flags) | • Icc: ~650 µA typ | $2.17 – $2.44 | [Mouser](https://www.mouser.com/ProductDetail/STMicroelectronics/MP34DT06JTR?qs=%252BEew9%252B0nqrDi3H2kv8Y1Xg%3D%3D)<br>[Digi-Key](https://www.digikey.com/en/products/detail/stmicroelectronics/MP34DT06JTR/9605993)<br>Lifecycle and stock status must be rechecked before reconsidering this non-baseline block |

#### Notes & Considerations

- **Power risk (Rev A coin cell):** all listed microphones draw on the order of **~0.65–1.3 mA when enabled**, which is too high for continuous operation. If implemented on Rev A, this block must be aggressively duty-cycled (short sampling windows at low cadence) or omitted.
- **Output expectation:** without calibration, results should be treated as **relative sound level** (quiet vs loud). Achieving meaningful absolute dBA accuracy requires calibration and enclosure/acoustic considerations.
- **Port location / PCB orientation:**
  - **Bottom-port** microphones couple sound through an opening beneath the package (via a PCB acoustic via/hole to the outside world). If mounted on the top side of the PCB, a bottom-port mic can still sense sound from the “top” of the product, but only if the PCB has a properly designed acoustic port path to that side (hole + keepout + gasket/mesh as needed).
  - Without a designed acoustic port, the mic will be heavily attenuated and sensitive to internal cavity effects; it will not reliably “listen through” solid PCB/enclosure material.
  - **Top-port** microphones simplify enclosure integration when the opening is on the same side as the component.

**Selected Baseline:** Omit from Rev A and Rev B baseline; reconsider only for a future externally powered variant with defined acoustic requirements
**Decision Status:** Frozen as not fitted in the baseline designs

### 3.6 Measurement Source and Placement Baseline

- SHTC3 is the authoritative product source for temperature and relative humidity.
- BME688 temperature and humidity inputs are reserved for BSEC/gas compensation and explicitly identified diagnostics.
- Place SHTC3 near ventilated ambient access and away from the BME688 heater, power converters, charger, radios, LED, and high-current copper.
- Give the BME688 gas port unobstructed ambient access and protect it from flux, cleaning residue, coating, adhesive, and enclosure outgassing.
- Align VEML7700 with the final optical window and shield it from the product's own LED and internal shadows.
- Validate sensor accuracy and response time on the assembled PCB and in the production-intent enclosure before freezing placement.



---

## 4) External Nonvolatile Memory

External flash is required in both revisions for secure OTA staging and is also
useful for buffering selected sensor history, crash records, and diagnostic data.
Nordic's nRF52840 Matter OTA flow requires an external secondary slot, and the
nRF52840 DK uses a 64-Mbit MX25R6435F device that is already supported by Zephyr.

### Capacity and Cost Comparison

| Capacity class | Usable capacity | Representative current pricing | Assessment |
|---|---:|---:|---|
| 16 Mbit | 2 MiB | [$1.48 at quantity 1](https://www.digikey.com/en/products/detail/macronix/MX25R1635FZUIL0/8416960) for a representative active MX25R part | Lowest cost, but a conservative OTA reserve consumes nearly the entire device and leaves no useful history margin |
| 32 Mbit | 4 MiB | [$2.01 at quantity 1](https://www.digikey.com/en/products/detail/macronix/MX25R3235FM1IL0/7915034) for a representative stocked MX25R part | Viable minimum, but leaves only about 2 MiB for data after the OTA reserve |
| 64 Mbit | 8 MiB | $2.89 at quantity 1 for the selected low-power-default part | Best balance: Nordic/Zephyr reference-device compatibility and roughly 6 MiB remaining after a 2 MiB OTA reserve |

The selected 64-Mbit device costs approximately $0.88 more than the representative
32-Mbit option and $1.41 more than the representative 16-Mbit option. That small
increment avoids a near-term capacity constraint and provides a standard,
well-supported software target. A 128-Mbit device is not justified by the intended
firmware or sensor-history volume.

### Selected Implementation

- **Part:** [Macronix MX25R6435FZNIL0](https://www.digikey.com/en/products/detail/macronix/MX25R6435FZNIL0/6558605)
- **Capacity/interface:** 64 Mbit (8 MiB), SPI/dual/quad I/O up to 80 MHz
- **Package:** 8-WSON, 6 × 5 mm, selected over WLCSP/DSBGA-style packages for prototype assembly and inspection
- **Supply:** 1.65–3.6 V; connect to 3V0_MAIN in Rev A and 3V3_MAIN in Rev B
- **Power mode:** L0 ordering option defaults to ultra-low-power mode; firmware shall also use deep power down between accesses
- **MCU connection:** Rev A uses the BL654 QSPI controller with SCK, CSN, and IO0–IO3. Rev B uses the device's standard SPI mode on the independent nRF5340 SPIM4 instance. The schematic pin baseline is P0.08/SCK, P0.09/MOSI, P0.10/MISO, and P0.11/CSN, all exposed by WT02C40C. WT02C40C also exposes the nRF5340 QSPI signal nets, but they are the same nets connected internally to nRF7002 and do not provide a second independently selectable QSPI bus.
- **Availability:** active, production-recommended family; 34,078 units at Digi-Key in the 2026-08-12 sourcing check

Reserve at least 2 MiB for the MCUboot/Matter OTA secondary slot until the signed
production build determines the exact partition. After allowing approximately
0.25 MiB for settings, crash records, and storage metadata, approximately 5.75 MiB
remains for sensor history. At one packed 32–48 byte record per minute, that is
approximately 87–131 days before circular overwrite, excluding filesystem overhead.
At a five-minute record cadence, the same allocation is approximately 14–21 months.

Firmware shall buffer records, use a wear-aware circular layout, and recover from
interrupted program/erase operations. The active image remains in the selected
host's internal flash so loss or corruption of the external device cannot become the
only boot path.

**Selected Baseline:** Macronix MX25R6435FZNIL0, populated in both revisions
**Decision Status:** Capacity, package, and part frozen; Rev A QSPI and Rev B SPIM4 interfaces frozen; exact firmware partition sizes pending production-image measurement

---

## 5) Power Management Strategy

This section records the power architecture selected by the completed [Power Architecture and Power Study](../architecture/Power_Architecture_and_Power_Study.md). Because Rev A and Rev B have fundamentally different power sources and load profiles, power management is tracked separately.

---

### 5.1 Selected Voltage Domains and Controls

- **Rev A – 3V0_MAIN:** 3.0 V from a TPS63900 buck-boost; supplies the BL654, SHTC3, VEML7700, external flash, logic, and one selectable BME688 source path.
- **Rev B – 3V3_MAIN:** 3.3 V from a TPS63802 buck-boost; supplies WT02C40C nRF5340 VDD, baseline sensors, external flash, logic, and one selectable BME688 source path.
- **Rev B – 3V3_WIFI_IN:** separately measurable 3.3 V feed to WT02C40C pin 17. The module's internal switch, controlled by nRF5340 P0.31, gates the nRF7002 supply; no external TPS22919 is required.
- **VDD_1V8_EVAL:** Populated TPS62840 1.8 V evaluation rail on pre-v1.0 boards. A break-before-make SPDT micro selector switches BME688 VDD between it and the main rail; BME688 VDDIO remains on the main logic rail.

Baseline sensors remain powered and use their specified sleep modes. Only the nRF7002 Wi-Fi section is hard power-gated. Both revisions use a normally-off ADC divider for coarse battery measurement. Rev B also routes BQ24074 PGOOD and CHG status to the MCU.

Pre-v1.0 schematics shall include normally shunted two-pin 2.54 mm male high-side current headers and separate two-pin 2.54 mm female local VDD/GND voltage headers at the battery/input, post-converter main rail, MCU/Thread/BLE branch, external-flash branch, BME688 VDD, SHTC3 VDD, VEML7700 VDD, and Rev B Wi-Fi rail. Firmware profile GPIOs shall be exposed so a PPK2 or oscilloscope can correlate heater, sensor, Thread/BLE, flash, and Wi-Fi activity with current traces. At v1.0 and later, the development headers may be replaced by the compact production test-point set after validation closes.

**Decision Status:** Baseline topology frozen; BME688 rail population and measured power model pending prototype validation

---

### 5.2 Rev A – Coin Cell Power Path (Matter-over-Thread)

#### Selected Implementation

- One CR2477-class 3 V, 1000 mAh Li-MnO₂ coin cell
- TI TPS63900 buck-boost set to 3.0 V
- Initial programmable input-current limit: 100 mA; verify the final setting against minimum cell voltage, efficiency, coin-cell pulse capability, holder resistance, and the approximately 41 mA worst-case output overlap
- External 32.768 kHz crystal for the BL654
- Macronix MX25R6435FZNIL0 QSPI NOR on 3V0_MAIN
- 100–220 µF low-leakage bulk-capacitor footprint in addition to converter and local decoupling
- Populated TPS62840 1.8 V evaluation rail and a break-before-make SPDT micro selector for BME688 VDD on pre-v1.0 hardware
- Normally shunted two-pin 2.54 mm male current headers plus separate two-pin 2.54 mm female local voltage headers at the complete-device input and major load branches on pre-v1.0 hardware

The conservative modeled battery life is approximately seven months with the BME688 running BSEC ULP. The supply must survive an approximately 41 mA worst-case overlap, although firmware shall avoid overlapping BME688 heater turn-on, high-power Thread/BLE radio activity, and routine flash writes.

**Selected Baseline (Rev A):** TPS63900 at 3.0 V from one CR2477
**Decision Status (Rev A):** Frozen for schematic baseline; cell-pulse and end-of-life validation required

---

### 5.3 Rev B – LiPo + USB-C Power Path (Thread + Wi-Fi)

#### Selected Implementation

| Component | Function | Selection basis |
|---|---|---|
| TI BQ24074 | 1S LiPo charger and dynamic power path | USB/battery switchover, battery supplement, batteryless startup, PGOOD/CHG status |
| TI TPS63802 | 3.3 V, 2 A buck-boost | Covers the LiPo discharge range and approximately 300 mA worst-case system peak with margin |
| TI TPS62840 | BME688 1.8 V evaluation rail | Populated on pre-v1.0 evaluation boards; 60 nA typical IQ, 1.8 V to 6.5 V input, 750 mA output capability, and efficient light-load operation |

The BQ24074 input-current limit is initially 500 mA and charge current is approximately 400–500 mA for a protected 2000 mAh LiPo. The 3.3 V rail is designed for at least 500 mA continuous, 1 A transient capability, and less than 200 mV droop at the Wi-Fi module.

The Rev B USB-C receptacle shall also route USB 2.0 D+ and D− to the
WT02C40C/nRF5340 USB device peripheral. The schematic shall include low-capacitance
ESD protection at the connector, VBUS sensing, and any series components required by
the current Nordic/Fanstel reference design. USB data is intended for service logs,
recovery, and wired firmware-update workflows; no USB Power Delivery controller is
required for the baseline.

**Selected Baseline (Rev B):** BQ24074 + TPS63802 + WT02C40C internal nRF7002 power switch
**Decision Status (Rev B):** Frozen for schematic baseline; thermal and transient validation required


---

## 6) Battery Strategy

### Candidate Comparison

| Revision | Chemistry | Capacity | Key Pros | Key Cons | Availability |
|--------|----------|----------|----------|----------|--------------|
| Rev A | CR2477 Li-MnO₂ coin cell | 1000 mAh nominal; 700 mAh conservative usable | Simple; replaceable; modeled seven-month baseline | Pulse capability and holder/contact resistance require validation | High |
| Rev B | Protected 1S LiPo with NTC preferred | 2000 mAh nominal; 1600 mAh conservative usable | Rechargeable; supports Wi-Fi peaks | Pack/connector selection depends on enclosure | High |

### Notes & Considerations
- Rev A life is dominated by the BME688 ULP profile.
- Rev B's provisional Thread/Wi-Fi-unavailable reference is approximately 15 months; scheduled non-Matter Wi-Fi events model about 10 months at one upload per four hours and five months at one upload per hour. Matter-over-Wi-Fi associated power must be remodeled with WT02C40C measurements.
- Final holder, LiPo pack, connector, and mechanical retention remain BOM/enclosure selections.

**Selected Baseline:** CR2477 for Rev A; protected 2000 mAh 1S LiPo for Rev B
**Decision Status:** Frozen electrically; exact mechanical parts pending

---

## 7) Antenna Strategy

### Candidate Comparison

| Approach | RF Scope | Key Pros | Key Cons | Certification Impact | Cost |
|--------|----------|----------|----------|----------------------|------|
| BL654 451-00001 integrated trace antenna | Rev A BLE / Thread | Pre-certified module path; no external RF matching network | Requires module-edge placement and antenna keepout | Low risk when vendor integration rules are followed | Included in module |
| WT02C40C integrated chip antennas | Rev B BLE / Thread / Wi-Fi | Both RF paths integrated and certified in one module | Requires the vendor's larger dual-radio keepout and enclosure validation | Lower risk than separate or discrete RF paths | Included in module |
| NFC PCB loop or external antenna | NFC commissioning | Low incremental BOM and passive operation | Exact geometry and matching depend on PCB/enclosure | Product-level validation required | $ |

### Notes & Considerations
- Rev A BLE/Thread and Rev B BLE/Thread/Wi-Fi antenna approaches are frozen through BL654 and WT02C40C respectively.
- Follow Ezurio and Fanstel edge-placement, ground, copper-keepout, and enclosure-clearance requirements during layout.
- Rev B NFC remains a PCB-loop or external-antenna schematic/layout selection using the WT02C40C NFC pins.

**Selected Baseline:** Rev A BL654 integrated trace antenna; Rev B WT02C40C dual chip antennas
**Decision Status:** Radio antennas frozen; Rev B NFC implementation pending schematic/layout selection

---

## 8) Programming, Debug & Power-Test Strategy

### Candidate Comparison

| Approach | Use Case | Key Pros | Key Cons | Cost Impact | Notes |
|--------|----------|----------|----------|-------------|-------|
| SWD interface | Development / debug | Required; standard | Requires access points | Low | Mandatory |
| Bed-of-nails | Manufacturing | Scalable; fast | Fixture cost | Medium | Required |
| Tag-Connect / proprietary | Development only | Compact | Expensive cables | High | Avoid if possible |
| Shunted two-pin 2.54 mm male current headers | Pre-v1.0 power profiling | Standard jumper installed for normal use; removal opens the high-side feed for series measurement with female DuPont leads | Adds connector area and contact resistance | Low | Header/shunt rated for the domain |
| Separate two-pin 2.54 mm female VDD/GND headers | Pre-v1.0 voltage probing | Accepts male DuPont leads, provides a short local return, and measures the DUT side of the current header | Socket contacts are less convenient for direct probe hooks | Low | No male pins, so a jumper shunt cannot be installed |
| Compact test points | v1.0+ production debug | Preserves voltage, signal, event, and manufacturing access with less area | Does not inherently preserve per-branch series-current insertion | Low | Retained after validation |
| Profile-event GPIO test points | Time correlation | Aligns heater, Thread/BLE, flash, sensor, and Wi-Fi states with current traces | Consumes temporary GPIO/test area | Low | At least two markers |

### Power-Measurement Partitioning

Rev A and Rev B describe feature architectures; v0.x and v1.x describe PCB maturity. Board versions below v1.0 shall retain the full measurement-header set. Once the required validation is complete, v1.0 and later boards may use the compact production test-point set.

The approximately 2 × 2 inch size target applies to production/v1.0-and-later
hardware. Pre-v1.0 evaluation boards may be larger where necessary to keep the
required headers, selector, and debug connections accessible and unambiguous.

The pre-v1.0 hardware shall support a Nordic PPK2, source-measure unit, Joulescope, or equivalent instrument in source or ampere-meter mode.

- Required complete-device access: battery input on both revisions and USB input on Rev B.
- Required post-converter access: 3V0_MAIN on Rev A and 3V3_MAIN on Rev B.
- Required individual branches: MCU/Thread/BLE, external serial flash, BME688 VDD, SHTC3 VDD, VEML7700 VDD, and Rev B 3V3_WIFI_IN.
- Put a two-pin 2.54 mm male pin header in each high-side DC feed, labeled SOURCE and LOAD, with a standard removable jumper shunt fitted for normal operation. Removing the shunt shall permit series-current measurement with female-ended 2.54 mm DuPont leads, without soldering or cutting traces.
- Put a separate two-pin 2.54 mm female socket header for VDD_DUT/GND near each domain. Sense VDD_DUT downstream of the current header, keep its ground close to the DUT, and connect voltage instruments with male-ended 2.54 mm DuPont leads.
- Do not combine VDD_INPUT, VDD_OUTPUT, and GND on one three-pin header. Do not populate male pins at voltage-measurement positions. Male current headers and female voltage headers are mandatory physical differentiation so the current shunt cannot be placed across VDD and ground; silkscreen shall reinforce this distinction.
- Select compact headers and shunts whose current rating and contact resistance cover the domain, including Rev B's approximately 300 mA-class peaks.
- Put the BME688 current header downstream of its SPDT main-rail/1.8 V selector so the same instrument connection measures either supply.
- Implement the BME selector as one break-before-make micro switch with an SPDT source-selection function, not two independently operated SPST DIP poles. Change it only with the board unpowered.
- If practical, a DPDT version may use the second pole to control TPS62840 enable, disabling the evaluation buck in the main-rail position. Otherwise account for its approximately 60 nA quiescent current during comparison.
- At v1.0 and later, retain compact test points for battery/input, main rail, external-flash VDD, BME688 VDD, ground, SWD, power-control/status signals, and profile GPIOs; Rev B also retains USB input and 3V3_WIFI_IN.
- Per-branch series-current insertion is not implied after a shunted header is removed. Any branch that still requires production current profiling shall retain an explicit removable link or fixture-accessible disconnect.

### Notes & Considerations

- Explicit goal to avoid expensive proprietary programming solutions.
- Header-based access is preferred on pre-v1.0 development boards; compact test-point access is preferred at v1.0 and later.
- Power access points must remain reachable on an unenclosed prototype and be compatible with the intended shunts, PPK2 leads, spring probes, or grabber leads.
- Schematic notes shall identify allowed external-injection states so rails cannot be back-powered or shorted together.

**Selected Baseline:** SWD + bed-of-nails programming; pre-v1.0 shunted high-side current headers with separate voltage headers; v1.0+ compact test points; profile-event GPIO access
**Decision Status:** Frozen at the architecture level; exact connector and test-pad geometry to be completed during schematic/layout

---

## 9) Selected Orderable Baseline and Availability Snapshot

The following table records the schematic-library and footprint baselines. Packaging
suffix substitutions require confirmation that the electrical function and land
pattern are identical. Inventory combines the 2026-08-12 baseline check with the
2026-08-15 Rev B module check and is not a lifecycle guarantee.

| Function | Selected orderable part | Package / antenna | Availability snapshot |
|---|---|---|---|
| Rev A MCU / BLE / Thread | Ezurio **451-00001** (BL654) | Module with integrated trace antenna | Active; Digi-Key 250, with substantially more stock visible at Mouser |
| Rev B MCU / BLE / Thread / Wi-Fi | Fanstel **WT02C40C** (nRF5340+nRF7002) | Combo module with two integrated chip antennas | Active; Digi-Key 183 and Fanstel direct availability in the 2026-08-15 check |
| Rev B Thread-only population option | Fanstel **BT40F** (nRF5340) | Footprint-compatible host module with integrated antenna | Active; Mouser 6,596 and Digi-Key 859 in the 2026-08-15 check; $10.83 direct at quantity 1 |
| Temperature / humidity | Sensirion **SHTC3-TR-2.5KS** | 4-DFN, 2 × 2 mm | Active; Digi-Key 92,809 |
| VOC / IAQ / pressure | Bosch Sensortec **BME688** | 8-LGA, 3 × 3 mm | Active; Digi-Key 9,183 |
| Ambient light | Vishay **VEML7700-TT** | 4-SMD | Active; Digi-Key 13,130 |
| External serial NOR | Macronix **MX25R6435FZNIL0** | 8-WSON, 6 × 5 mm, low-power default; QSPI in Rev A and SPIM4 in Rev B | Active; Digi-Key 34,078 |
| Rev A main converter | TI **TPS63900DSKR** | 10-WSON | Active; Digi-Key 32,261 |
| BME688 1.8 V evaluation converter | TI **TPS62840DLCR** | 8-VSON-HR | Active; Mouser 5,867; selected instead of the smaller DSBGA for easier prototype assembly |
| Rev B main converter | TI **TPS63802DLAR** | 10-VSON-HR | Active; Digi-Key 9,507 |
| Rev B charger / power path | TI **BQ24074RGTR** | 16-VQFN, 3 × 3 mm | Active; Digi-Key 11,717 |
| Rev A cell | Panasonic **CR2477** | Replaceable cell; holder pending | Active; Digi-Key 81,306 |

The exact CR2477 holder, protected LiPo pack, USB-C receptacle, LFXO crystal,
selector, measurement headers, shunts, passives, magnetics, and protection parts
remain schematic/BOM selections and therefore cannot yet be checked by exact MPN.

---

## 10) Summary & Next Steps

- Rev A and shared-block baseline selections are frozen:
  - Ezurio BL654 for Rev A
  - Fanstel WT02C40C nRF5340+nRF7002 combo module for Rev B
  - SHTC3, BME688, and VEML7700
  - MX25R6435FZNIL0 64-Mbit external serial NOR: QSPI on Rev A, SPIM4 on Rev B
  - BME688 pressure output; no separate barometric sensor
  - no baseline microphone/sound block
  - TPS63900 + CR2477 for Rev A
  - BQ24074 + TPS63802 + WT02C40C internal Wi-Fi switch + 2000 mAh LiPo for Rev B
- The BME688 component is frozen, but its production VDD source remains an explicit pre-v1.0 measurement decision between the main rail and an efficient 1.8 V buck, selected by a populated break-before-make SPDT micro switch.
- Both revisions support BLE commissioning and non-Matter BLE Local Mode. Rev A uses Matter-over-Thread. Rev B uses the WT02C40C for Matter-over-Wi-Fi or a separate Thread firmware build; nRF7002 uses the nRF5340's sole QSPI controller and dedicated CSN net, so external flash uses the independent SPIM4 peripheral.
- Pre-v1.0 schematic/layout shall include shunted two-pin 2.54 mm male whole-device and per-load current headers, including external flash, separate two-pin 2.54 mm female voltage headers, and profile-event GPIOs; v1.0 and later shall retain the compact production test-point set.
- Next:
  - begin schematic capture and select exact passives, magnetics, battery holder, connectors, and protection parts;
  - download and archive the current WT02C40C specification, official ECAD footprint, STEP model, and evaluation/reference schematic from Fanstel's [download page](https://www.fanstel.com/download-document), then record their revisions in the library component;
  - create and review the WT02C40C symbol/footprint, complete its full pin allocation, route USB 2.0 data, and preserve the BT40F Thread-only population option;
  - complete NFC antenna, programming/test geometry, status LED, and populated multifunction-button selections during schematic/layout;
  - verify the modeled power figures on prototype hardware;
  - revisit optional Rev B multispectral or 1.8 V sensor population only if the product scope requires it.
