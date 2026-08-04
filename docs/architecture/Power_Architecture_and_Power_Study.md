# MatterSense – Power Architecture and Power Study

**Status:** Architecture decisions complete; schematic implementation and prototype validation remain
**Last updated:** 2026-08-04

---

## 1. Purpose

This document defines and closes the pre-schematic power architecture for MatterSense Rev A and Rev B. It:

- maps every baseline load to a power rail;
- defines representative operating profiles;
- estimates average and peak current;
- estimates battery life with stated derating and conversion losses;
- selects the regulator, charger/power-path, load-switch, and battery classes;
- identifies the measurements required to validate the estimates on hardware.

The results are planning estimates, not guaranteed battery-life specifications. Datasheet values are typical unless a maximum is explicitly stated. Firmware, RF conditions, temperature, battery age, PCB leakage, component tolerances, and converter efficiency will change measured results.

---

## 2. Final Architecture Decisions

| Item | Rev A decision | Rev B decision | Status |
|---|---|---|---|
| Battery | One Panasonic CR2477-class 3 V, 1000 mAh Li-MnO₂ coin cell | Protected single-cell 3.7 V nominal, 2000 mAh LiPo with 10 kΩ NTC preferred | Frozen electrically; final holder/pack is mechanical/BOM work |
| Primary rail | 3.0 V regulated | 3.3 V regulated | Frozen |
| Main converter | [TI TPS63900](https://www.ti.com/lit/ds/symlink/tps63900.pdf) buck-boost | [TI TPS63802](https://www.ti.com/lit/ds/symlink/tps63802.pdf) 2 A buck-boost | Frozen |
| USB/LiPo power path | Not applicable | [TI BQ24074](https://www.ti.com/lit/ds/symlink/bq24074.pdf), 500 mA input limit and approximately 400–500 mA charge current | Frozen for schematic baseline |
| Wi-Fi rail | Not applicable | 3.3 V switched branch from 3V3_MAIN | Frozen |
| Wi-Fi switch | Not applicable | [TI TPS22919](https://www.ti.com/lit/gpn/TPS22919), controlled by BL654 | Frozen |
| 1.8 V rail | Omitted | Omitted from baseline; DNP [TPS7A0218](https://www.ti.com/lit/ds/symlink/tps7a02.pdf) option only if a 1.8 V sensor is fitted | Frozen |
| Sensor gating | No separate load switch; use device sleep modes | Baseline sensors remain powered and use sleep modes | Frozen |
| Battery measurement | MCU ADC through a normally-off switched divider | MCU ADC through a normally-off switched divider; charger PGOOD and CHG also routed to MCU | Frozen |
| Fuel gauge | Not fitted | Not fitted; reserve test/DNP provision only if later accuracy requirements justify it | Frozen |
| Low-frequency clock | External 32.768 kHz crystal on BL654 | External 32.768 kHz crystal on BL654 | Frozen |
| Optional sound block | Not fitted | Not fitted in baseline; future externally powered option only | Frozen for baseline |

The current Rev B hardware-selection document identifies the Fanstel WM02C as the frozen Wi-Fi module. The power architecture is based on the nRF7002 electrical envelope and therefore also remains valid if another nRF7002 module, such as the MinewSemi MS14SF1-1N02AIR, is substituted after a sourcing review.

---

## 3. Why These Decisions Were Made

### 3.1 Rev A: 3.0 V Buck-Boost Instead of Direct Coin-Cell Power

All Rev A baseline loads operate at 3.0 V:

- BL654 VDD: 1.7 V to 3.6 V;
- SHTC3: 1.62 V to 3.6 V;
- BME688: 1.71 V to 3.6 V;
- VEML7700: 2.5 V to 3.6 V.

A direct coin-cell rail would have zero converter loss, but it would:

- expose all loads to battery-voltage and pulse-droop variation;
- reach the VEML7700 2.5 V minimum before the cell is fully discharged;
- produce less repeatable brownout and battery-life behavior;
- make BME688 heater and radio-current interactions more difficult to control.

The TPS63900 provides a stable 3.0 V rail across the useful cell range, has 75 nA typical quiescent current, exceeds 90% efficiency at a 10 µA load, and supports programmable input-current limiting. Its power penalty is small compared with the 90 µA BME688 ULP load.

The 3.0 V setpoint is preferred over 3.3 V because it is compatible with every baseline load, reduces energy per active event, and reduces boost ratio and input current near coin-cell end of life.

### 3.2 Rev B: One 3.3 V Converter With a Switched Wi-Fi Branch

The LiPo voltage crosses above and below 3.3 V during discharge, so a buck-only or boost-only solution cannot hold the rail across the full battery range. The TPS63802 provides:

- buck, buck-boost, and boost operation;
- 2 A output capability for the intended operating range;
- 11 µA typical operating quiescent current;
- substantial margin above the approximately 300 mA calculated system peak.

A separate Wi-Fi converter is not required. A TPS22919 load switch isolates the Wi-Fi module when off, limits inrush through controlled turn-on, and introduces only about 23 mV typical drop at 260 mA from its 90 mΩ typical on-resistance.

### 3.3 No Baseline 1.8 V Rail

No selected baseline component requires 1.8 V. Running the BME688 from a dedicated 1.8 V rail does not justify the additional converter, capacitors, routing, enable logic, and possible level-domain complexity. Rev B may include a DNP 1.8 V LDO footprint for an optional ENS160 or multispectral sensor. The baseline I²C buses remain at the primary rail voltage and need no level shifter.

### 3.4 Sensors Remain Powered

The baseline sensors already have low-current sleep modes. Keeping them powered:

- avoids extra switch leakage and BOM;
- avoids I²C back-power paths;
- preserves BME688/BSEC operating history and avoids repeated stabilization;
- simplifies wake sequencing.

The firmware must explicitly return the SHTC3 to sleep and the VEML7700 to shutdown after use. The BME688 remains powered and runs the BSEC ULP profile.

---

## 4. Top-Level Power Architectures

### 4.1 Rev A

CR2477 → TPS63900 3.0 V buck-boost → BL654, SHTC3, BME688, and VEML7700. A separate normally-off divider connects raw battery voltage to the BL654 ADC only while a measurement is taken.

### 4.2 Rev B

USB-C 5 V and the 1S LiPo connect to the BQ24074 power-path charger. Its OUT node feeds the TPS63802 3.3 V buck-boost. The BL654 and baseline sensors use 3V3_MAIN; a TPS22919 creates 3V3_WIFI_SW for the WM02C. A DNP TPS7A0218 may create 1.8 V for future optional sensors.

With USB present, the power path powers the system and charges the battery. Without USB, the battery supplies OUT through the internal battery FET. The battery can supplement the input during a load transient, and the system can start from USB with a missing or deeply discharged battery.

---

## 5. Rail Map

| Function | Baseline component | Rev A rail | Rev B rail | Low-power state |
|---|---|---:|---:|---|
| MCU / BLE | Ezurio BL654 | 3V0_MAIN | 3V3_MAIN | System ON idle; System OFF only for storage |
| Temperature / humidity | Sensirion SHTC3 | 3V0_MAIN | 3V3_MAIN | Explicit sleep command |
| VOC / IAQ / pressure | Bosch BME688 | 3V0_MAIN | 3V3_MAIN | BSEC ULP / sensor sleep between heater events |
| Ambient light | Vishay VEML7700 | 3V0_MAIN | 3V3_MAIN | Software shutdown between readings |
| Wi-Fi | Fanstel WM02C | — | 3V3_WIFI_SW | Hard off through TPS22919 |
| Optional 1.8 V sensor | ENS160 or multispectral sensor | Not supported in baseline | VDD_1V8_DNP | LDO disabled when option absent/off |
| Battery sensing | Resistor divider + ADC filter | Raw coin cell, switched | Raw LiPo, switched | Divider normally disconnected |
| Status LED | BOM/layout selection | 3V0_MAIN | 3V3_MAIN | Off except short user-visible events |

---

## 6. Electrical Inputs Used in the Model

| Load | State | Planning value | Source / treatment |
|---|---|---:|---|
| BL654 | System ON idle, full RAM + RTC + external LFXO | 2.6 µA | [Ezurio BL654 datasheet](https://www.ezurio.com/documentation/datasheet-bl654) |
| BL654 | System OFF | 0.4 µA | Datasheet typical |
| BL654 | BLE RX | 4.6 mA peak | Datasheet, DC/DC on |
| BL654 | BLE TX at 0 dBm | 4.8 mA peak | Datasheet, DC/DC on |
| BL654 | BLE TX at +8 dBm | 14.1 mA peak | Datasheet maximum-radio case |
| SHTC3 | Sleep | 0.3 µA typical | [SHTC3 datasheet](https://sensirion.com/resource/datasheet/shtc3) |
| SHTC3 | Normal measurement | 430 µA for 10.8 ms typical | Used to derive 0.38 µA at one sample per minute |
| BME688 | Sleep | 0.15 µA typical | [BME688 datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) |
| BME688 | BSEC ULP | 90 µA average | 300 s IAQ update interval |
| BME688 | Heater operating | 12 mA typical, 13 mA max | At the datasheet heater condition |
| BME688 | Heater turn-on peak | 17 mA typical, 18 mA max | First milliseconds of hotplate turn-on |
| VEML7700 | Shutdown | 0.5 µA typical | [VEML7700 datasheet](https://www.vishay.com/docs/84286/veml7700.pdf) |
| VEML7700 | Active, PSM disabled | 45 µA for 100 ms | Used to derive 0.57 µA at one sample per minute |
| TPS63900 | Operating quiescent current | 0.075 µA typical | Included in Rev A |
| TPS63802 | Operating quiescent current | 11 µA typical | Included in Rev B |
| BQ24074 | Battery sleep current, USB absent | 4.3 µA typical, 6.5 µA max at stated condition | Conservative 6.5 µA used |
| TPS22919 | Off-state current | 2 nA typical | Wi-Fi module isolated |
| WM02C / nRF7002 | TX | 191 mA at 2.4 GHz; 260 mA at 5 GHz | 260 mA used for rail design |
| WM02C / nRF7002 | RX listen | approximately 56–60 mA | Peak/active state |
| WM02C / nRF7002 | Connected power save | approximately 4 mA planning value | Used only for USB-mode range |
| CR2477 | Nominal rating | 3 V, 1000 mAh, 0.2 mA standard continuous drain | [Panasonic CR2477](https://energy.panasonic.com/na/business/products/lithium/coin-cr-standard/models/CR2477) |

The BME688 ULP current is intentionally used without claiming that it scales linearly with supply voltage. It is the dominant uncertainty and must be measured with the actual BSEC configuration.

---

## 7. Operating and Duty-Cycle Assumptions

### 7.1 Shared Battery-Mode Assumptions

| Parameter | Baseline assumption |
|---|---:|
| SHTC3 cadence | One normal-mode T/RH reading per 60 s |
| VEML7700 cadence | One 100 ms lux conversion per 60 s; shutdown otherwise |
| BME688 cadence | BSEC ULP, IAQ update every 300 s |
| BLE advertising interval | 2 s |
| BLE advertising contribution | 10 µA average planning allowance |
| MCU/sensor-service overhead | 2 µA average planning allowance |
| LED use | Excluded from normal operation; brief commissioning/status indications only |
| ADC divider | Normally off; sampled on boot, periodically, and around low-battery events |
| Miscellaneous leakage | 1 µA Rev A; 2 µA Rev B |
| Design contingency | 15% added to the modeled baseline |

The 10 µA BLE allowance is an engineering assumption for a 2 s, three-channel advertising configuration at 0 dBm. It must be replaced with Power Profiler measurements after the Zephyr Bluetooth configuration is frozen.

### 7.2 Battery Capacity and Efficiency

| Parameter | Rev A | Rev B |
|---|---:|---:|
| Nominal capacity | 1000 mAh | 2000 mAh |
| Conservative usable fraction | 70% | 80% |
| Conservative usable capacity | 700 mAh | 1600 mAh |
| Converter efficiency used | 90% | 90% |
| Nominal battery voltage for energy conversion | 3.0 V | 3.7 V |

Rev A derating is larger because a coin cell is more sensitive to pulse load, temperature, age, internal resistance, and cutoff voltage. The estimate does not attempt to claim the full printed capacity under the MatterSense load profile.

### 7.3 Rev B Wi-Fi Event Assumption

The battery-mode Wi-Fi estimate uses:

- 100 mA average at the 3.3 V Wi-Fi rail;
- 10 s from power-on through association, upload, acknowledgement, and shutdown;
- cadence varied in the results table;
- a separate 260 mA instantaneous peak requirement for rail design.

This event model is deliberately simple. Association time and current vary materially with access point, signal strength, retries, security exchange, DHCP behavior, firmware, and 2.4/5 GHz use. Hardware validation must record coulombs per complete upload event, not only peak current.

---

## 8. Calculation Method

For a periodic load:

**Iavg = Isleep + (Iactive − Isleep) × tactive / T**

For a converter-fed load:

**Ibattery ≈ (Vrail × Irail) / (Vbattery × efficiency) + Iq**

Estimated life:

**Life (hours) = usable capacity (mAh) / average battery current (mA)**

One average month is treated as 730 hours. Battery self-discharge and calendar aging are not added as a separate current; they are partly covered by the usable-capacity derating.

---

## 9. Rev A Results

### 9.1 Typical Current Budget

| Contributor | Average current at 3.0 V rail |
|---|---:|
| BL654 System ON idle with external LFXO | 2.60 µA |
| BLE advertising allowance | 10.00 µA |
| MCU and I²C service allowance | 2.00 µA |
| SHTC3 at one reading per minute | 0.38 µA |
| VEML7700 at one reading per minute | 0.57 µA |
| BME688 BSEC ULP | 90.00 µA |
| TPS63900 quiescent current | 0.08 µA |
| Battery divider, pull resistors, and PCB leakage allowance | 1.00 µA |
| **Typical modeled subtotal** | **106.63 µA** |
| **With 15% contingency** | **122.62 µA** |

At 90% conversion efficiency, the conservative result is approximately **136 µA battery-equivalent average current**.

### 9.2 Battery-Life Estimate

| Case | Capacity basis | Load basis | Estimated life |
|---|---:|---:|---:|
| Nominal / optimistic | 1000 mAh | Typical subtotal | 8440 h / **11.6 months** |
| Conservative design case | 700 mAh | 15% contingency | 5140 h / **7.0 months** |
| IAQ sensor omitted/disabled | 700 mAh | Same assumptions without BME688 ULP | approximately **45 months**, normally shelf-life limited first |

The Rev A multi-month target is met with the BME688 ULP profile. The BME688 consumes about 73% of the conservative rail budget and is the primary battery-life lever.

### 9.3 Rev A Mode Budget

| System mode | Expected current | Notes |
|---|---:|---|
| Storage, electronics connected | Target ≤3 µA | BL654 System OFF plus sleeping sensors and converter; verify on board |
| Between scheduled events | Approximately 4–6 µA | BL654 System ON idle plus sleeping sensors |
| BLE advertising | Approximately 5–15 mA instantaneous | Average covered by advertising allowance |
| SHTC3/lux sampling | Approximately 4–6 mA while MCU is active | Short duration |
| BME688 heater event | 12 mA typical; 18 mA max turn-on peak, plus MCU | Long enough that the battery, not only a capacitor, must support it |

### 9.4 Rev A Peak-Current Decision

The worst unscheduled overlap is approximately:

- BME688 heater peak: 18 mA;
- BL654 +8 dBm TX: 14.1 mA;
- MCU and other active loads: approximately 5 mA;
- total: approximately 37 mA.

Firmware shall avoid scheduling a BLE high-power radio event during BME688 heater turn-on. The normal target peak is therefore less than approximately 25 mA. The power path shall nevertheless survive the 37 mA overlap without reset.

Rev A schematic requirements:

- configure TPS63900 input-current limit initially for 50 mA;
- fit at least 22 µF effective ceramic output capacitance per converter guidance;
- provide an additional 100–220 µF low-leakage bulk-capacitor footprint near the main rail;
- place local 0.1 µF and device-recommended bulk capacitors at each IC;
- expose battery voltage and 3V0_MAIN test points;
- verify cold, aged-cell, and end-of-life pulse behavior on hardware.

A bulk capacitor can cover fast radio/load-step response, but it cannot supply the complete BME688 heater interval. Coin-cell chemistry and holder/contact resistance must be validated with the actual heater profile.

---

## 10. Rev B Results

### 10.1 Battery-Mode Baseline Without Wi-Fi Events

| Contributor | Battery-equivalent average current |
|---|---:|
| BL654, BLE, MCU service, and baseline sensors through TPS63802 | approximately 107 µA |
| TPS63802 quiescent current | 11.0 µA |
| BQ24074 battery sleep current | 6.5 µA |
| Miscellaneous allowance included above | 2.0 µA |
| **Subtotal before contingency** | **approximately 124 µA** |
| **With 15% contingency** | **approximately 143 µA** |

With 1600 mAh usable capacity, the Wi-Fi-gated baseline is approximately **11,200 hours or 15.4 months**.

### 10.2 Wi-Fi Cadence Sensitivity

| Battery-mode policy | Wi-Fi average contribution | Total estimated battery current | Estimated life |
|---|---:|---:|---:|
| Wi-Fi never scheduled; BLE only | 0 µA | 143 µA | **15.4 months** |
| One 10 s upload every 4 h | 69 µA | 212 µA | **10.4 months** |
| One 10 s upload every 1 h | 275 µA | 418 µA | **5.2 months** |
| One 10 s upload every 15 min | 1101 µA | 1244 µA | **1.8 months** |

The default Rev B battery policy shall therefore keep Wi-Fi hard-off and schedule no more than one upload per hour. A four-hour default provides substantially better margin until measured event energy is available. User-requested or retry traffic may temporarily exceed the nominal cadence.

### 10.3 USB-Powered Mode

With Wi-Fi associated in power-save mode, the pre-schematic typical system estimate is approximately 5–15 mA at 3.3 V, excluding LiPo charge current. Short TX bursts can approach the peak budget below.

USB/power-path requirements:

- two 5.1 kΩ Rd resistors on USB-C CC1 and CC2 for a 5 V sink;
- no USB Power Delivery controller is required for the baseline;
- set the BQ24074 input-current limit to 500 mA unless Type-C current advertisement is actively detected;
- set LiPo charge current to approximately 400–500 mA for a 2000 mAh pack;
- route PGOOD and CHG status to the MCU;
- connect and validate the pack NTC/TS network;
- provide the BQ24074 thermal copper area required by its layout guidance.

The BQ24074 dynamic power path reduces charge current as system demand rises and allows battery supplement during peaks. At a 500 mA input limit, the system has ample average USB power; thermal regulation, not input wattage, is expected to set practical charge rate.

### 10.4 Rev B Peak-Current Budget

| Simultaneous load | Peak planning value |
|---|---:|
| nRF7002 5 GHz TX | 260 mA |
| BL654 +8 dBm TX | 14.1 mA |
| BME688 heater turn-on | 18 mA |
| MCU, sensors, and margin | 10–20 mA |
| **Worst planning total** | **approximately 302–312 mA** |

The 3V3_MAIN converter and power path shall be designed for at least:

- 500 mA continuous design capacity;
- 1 A transient capability;
- less than 200 mV rail droop at the Wi-Fi module during the characterized load step.

The TPS63802 and TPS22919 provide comfortable silicon current margin. Layout and capacitance remain critical.

Rev B schematic/layout requirements:

- start with 100–220 µF low-ESR bulk capacitance on 3V3_WIFI_SW;
- provide the converter-required input/output capacitors plus module-local 22 µF, 10 µF, and 0.1 µF placements as applicable;
- keep the high-di/dt Wi-Fi current loop short and separate from sensor ground returns;
- use a wide 3.3 V path and uninterrupted ground plane;
- connect TPS22919 QOD so the Wi-Fi rail discharges when disabled;
- ensure the MCU control pin defaults low during reset;
- design for 260 mA even if firmware initially limits Wi-Fi to 2.4 GHz.

Firmware should avoid overlapping Wi-Fi startup/TX with the BME688 heater where practical, but correctness shall not depend on event serialization.

---

## 11. Battery Monitoring and Protection Behavior

### 11.1 ADC Divider

Both revisions shall use a normally-off, high-side-switched resistor divider into a BL654 SAADC input. The divider shall:

- tolerate 3.3 V maximum coin-cell voltage in Rev A and 4.2 V LiPo voltage in Rev B;
- limit ADC pin voltage below its configured full-scale and below MCU supply rails;
- include a small capacitor at the ADC input to provide charge for sampling;
- allow sufficient settling time before conversion;
- draw negligible current when disabled.

Exact resistor, capacitor, and switch values are schematic-stage selections. A continuous divider that consumes more than 0.5 µA is not acceptable for Rev A.

### 11.2 Firmware Policy

- sample open-circuit battery voltage at boot and periodically;
- sample before and during a BME688 heater event to estimate source resistance/droop;
- in Rev A, reduce radio TX power and/or disable IAQ heater operation before the rail becomes unstable;
- in Rev B, report USB-present and charging status using PGOOD and CHG;
- debounce charger status and ADC readings in firmware;
- determine final warning and shutdown thresholds from characterized discharge curves, not nominal voltage alone.

No fuel gauge is required because the HRS only requires coarse state-of-charge estimation. A gauge may be added in Rev B only if field testing shows that voltage-based LiPo estimation is inadequate.

---

## 12. External 32.768 kHz Crystal Decision

The BL654 datasheet reports approximately:

- 3.1 µA System ON idle using the internal LFRC;
- 2.6 µA using an external 32.768 kHz LFXO.

The approximately 0.5 µA saving is small relative to the BME688, but the LFXO also improves radio timing and removes periodic RC calibration overhead. Both revisions shall populate the external LFXO unless later pin or layout constraints outweigh the benefit.

---

## 13. Power Verification Plan

The following measurements are required before battery-life numbers are treated as verified.

### 13.1 Rev A

1. Measure electronics-only storage current at 25 °C; pass target ≤3 µA.
2. Measure System ON idle with RTC, full firmware image, and all sensors sleeping; pass target ≤7 µA.
3. Measure charge per three-channel BLE advertising event at the selected interval and TX power.
4. Measure SHTC3 and VEML7700 event charge at their final cadence.
5. Measure one complete BME688 ULP cycle and long-term average with the selected BSEC version/configuration.
6. Capture 3V0_MAIN and raw-cell droop during BME688 heater start and BLE TX.
7. Repeat peak tests with a fresh cell, a partially discharged cell, and an aged/high-resistance cell at low indoor temperature.
8. Run a long-duration coulomb-counting test and compare against the model.

### 13.2 Rev B

1. Verify battery-only, USB-only with no battery, USB with battery, and source-transition behavior.
2. Verify charger thermal behavior at 400–500 mA charge current.
3. Measure Wi-Fi rail inrush and confirm clean startup through TPS22919.
4. Measure complete Wi-Fi event energy from rail enable through association, upload, acknowledgement, and rail discharge.
5. Test weak-signal and retry cases at 2.4 GHz and 5 GHz.
6. Capture 3V3_WIFI_SW minimum voltage during 260 mA-class TX bursts.
7. Verify that an unpopulated Wi-Fi module does not affect BLE/sensor operation.
8. Confirm battery-life policy using measured event energy and update the cadence table.

### 13.3 Instruments and Capture

Use a source-measure unit, Nordic Power Profiler Kit, Joulescope, or equivalent instrument capable of resolving both sub-microamp sleep current and hundreds-of-milliamp RF transients. A single handheld DMM average is not sufficient.

---

## 14. Schematic Handoff Checklist

The power study is complete enough to begin schematic capture with the following constraints:

- [x] Rev A primary rail fixed at 3.0 V.
- [x] Rev A TPS63900 buck-boost selected.
- [x] Rev A CR2477 battery class selected.
- [x] Rev B primary rail fixed at 3.3 V.
- [x] Rev B BQ24074 power-path charger selected.
- [x] Rev B TPS63802 main converter selected.
- [x] Rev B TPS22919 Wi-Fi load switch selected.
- [x] Baseline 1.8 V rail removed.
- [x] Baseline sensor power gating removed.
- [x] Battery ADC and charger-status requirements defined.
- [x] Peak-current and bulk-capacitance targets defined.
- [x] Battery-mode firmware cadences defined for estimation.
- [ ] Select exact inductors/capacitors and verify effective capacitance at bias.
- [ ] Select exact CR2477 holder and protected 2000 mAh LiPo pack.
- [ ] Complete converter loop/layout review against vendor reference designs.
- [ ] Replace modeled values with prototype measurements.

---

## 15. Conclusions

1. **Rev A is feasible from one CR2477.** The conservative estimate is approximately seven months with all baseline sensors, including BME688 ULP IAQ.
2. **The BME688 dominates Rev A energy.** BLE optimization is useful, but changing IAQ cadence or disabling IAQ has a much larger effect.
3. **A regulated 3.0 V Rev A rail is the best system trade.** The low-Iq TPS63900 removes rail variability and preserves usable cell range with little overhead.
4. **Rev B requires a true buck-boost and a hard Wi-Fi gate.** TPS63802 plus TPS22919 meets the current and leakage targets.
5. **Rev B battery life is a firmware-policy result.** The model ranges from approximately 15 months with Wi-Fi off to 1.8 months with 15-minute uploads. Hourly or slower battery-mode uploads satisfy the multi-month goal.
6. **No baseline 1.8 V rail or sensor load switches are justified.** They remain optional/DNP only for future Rev B sensor variants.
7. **The optional sound block is omitted from both baseline revisions.** A continuously active digital microphone would materially increase battery load and still requires acoustic/mechanical definition.
8. **Component selection can proceed to schematic capture.** Remaining risks are validation items—coin-cell pulse behavior, actual BME688 ULP energy, Wi-Fi event charge, and rail transient response—not unresolved topology decisions.

---

## 16. Primary References

- [Ezurio BL654 datasheet](https://www.ezurio.com/documentation/datasheet-bl654)
- [Sensirion SHTC3 datasheet](https://sensirion.com/resource/datasheet/shtc3)
- [Bosch BME688 datasheet](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf)
- [Vishay VEML7700 datasheet](https://www.vishay.com/docs/84286/veml7700.pdf)
- [Fanstel WM02C product specifications](https://fanstel.squarespace.com/s/WM02C-Product-Specifications-3py2.pdf)
- [TI TPS63900 datasheet](https://www.ti.com/lit/ds/symlink/tps63900.pdf)
- [TI TPS63802 datasheet](https://www.ti.com/lit/ds/symlink/tps63802.pdf)
- [TI BQ24074 datasheet](https://www.ti.com/lit/ds/symlink/bq24074.pdf)
- [TI TPS22919 datasheet](https://www.ti.com/lit/gpn/TPS22919)
- [TI TPS7A02 datasheet](https://www.ti.com/lit/ds/symlink/tps7a02.pdf)
- [Panasonic CR2477 product data](https://energy.panasonic.com/na/business/products/lithium/coin-cr-standard/models/CR2477)

---

## 17. Revision Notes

| Date | Change |
|---|---|
| 2026-08-04 | Completed rail selection, topology and component decisions, duty-cycle assumptions, current budgets, battery-life estimates, peak analysis, schematic requirements, and validation plan. |
