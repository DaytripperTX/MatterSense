# MatterSense – Power Architecture and Power Study

**Status:** Baseline architecture selected; BME688 supply-voltage comparison, schematic implementation, and prototype validation remain
**Last updated:** 2026-08-12

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
| BME688 1.8 V evaluation | Pre-v1.0 prototypes shall populate a [TPS62840](https://www.ti.com/lit/ds/symlink/tps62840.pdf) 1.8 V buck and a break-before-make SPDT micro selector between 3V0_MAIN and 1.8 V; VDDIO remains on 3V0_MAIN | Same provision between 3V3_MAIN and 1.8 V; VDDIO remains on 3V3_MAIN | Production rail and removal of evaluation hardware pending measured comparison |
| Sensor gating | No separate load switch; use device sleep modes | Baseline sensors remain powered and use sleep modes | Frozen |
| Battery measurement | MCU ADC through a normally-off switched divider | MCU ADC through a normally-off switched divider; charger PGOOD and CHG also routed to MCU | Frozen |
| Fuel gauge | Not fitted | Not fitted; reserve test/DNP provision only if later accuracy requirements justify it | Frozen |
| Power profiling access | Board versions below v1.0 shall use shunted two-pin 2.54 mm male series-current headers plus separate two-pin 2.54 mm female VDD/GND voltage headers | Same, plus separate Wi-Fi and USB/battery-path access; v1.0 and later retain compact test points after validation | Required for schematic/layout |
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

### 3.3 BME688 1.8 V Decision Reopened for Measurement

No selected baseline component requires 1.8 V, but that is not sufficient reason to close the question for the dominant load. Bosch states that BME688 power efficiency, performance, and heat dissipation scale with supply voltage and that the device is optimized for 1.8 V. More importantly, the published 90 µA BSEC ULP average, 12 mA heater current, and related gas-mode values are characterized at VDD ≤ 1.8 V. Equivalent gas-mode current is not specified at 3.0 V or 3.3 V.

The prototype shall therefore support an A/B comparison without requiring a PCB respin:

- Pre-v1.0 evaluation hardware shall populate the dedicated TPS62840-class 1.8 V buck and a user-operable selector so BME688 VDD can be changed between the main rail and 1.8 V without soldering.
- The selector shall be one mechanical break-before-make micro switch in a DIP/slide-style package that provides an SPDT source-selection function. A DPDT part is permitted if its second pole controls TPS62840 EN so the buck is disabled in the main-rail position. Two independently operated SPST DIP sections are not acceptable because an invalid switch state could connect the main rail and 1.8 V output together.
- BME688 VDDIO and the I²C pull-ups shall remain on the main logic rail, so no level shifter is required.
- The TPS62840 has 60 nA typical operating quiescent current, an input range of 1.8 V to 6.5 V, and sufficient peak-current capability for the BME688 heater.
- The selector common shall feed BME688 VDD, and its two throws shall connect only to the main rail and VDD_1V8_EVAL. The switch shall be changed only while the board is unpowered; firmware shall reinitialize the BME688 after any supply change.

Both supply paths shall be functional on pre-v1.0 evaluation builds. The production choice is not frozen until identical BSEC ULP profiles are measured at both voltages. The comparison shall use battery-input charge per complete cycle and long-term average current, not BME688 rail current alone, so converter losses are included. Once the choice is validated, v1.0 and later hardware may hardwire the selected rail and omit the selector and unused evaluation circuitry.

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

CR2477 → TPS63900 3.0 V buck-boost → BL654, SHTC3, and VEML7700. On pre-v1.0 evaluation hardware, BME688 VDD is supplied through a populated break-before-make SPDT selector from either 3V0_MAIN or a populated TPS62840 1.8 V evaluation rail; BME688 VDDIO remains on 3V0_MAIN. A separate normally-off divider connects raw battery voltage to the BL654 ADC only while a measurement is taken.

### 4.2 Rev B

USB-C 5 V and the 1S LiPo connect to the BQ24074 power-path charger. Its OUT node feeds the TPS63802 3.3 V buck-boost. The BL654, SHTC3, and VEML7700 use 3V3_MAIN; a TPS22919 creates 3V3_WIFI_SW for the WM02C. On pre-v1.0 evaluation hardware, BME688 VDD is selectable through a populated break-before-make SPDT switch from 3V3_MAIN or a populated TPS62840 1.8 V evaluation rail, while BME688 VDDIO remains on 3V3_MAIN.

With USB present, the power path powers the system and charges the battery. Without USB, the battery supplies OUT through the internal battery FET. The battery can supplement the input during a load transient, and the system can start from USB with a missing or deeply discharged battery.

---

## 5. Rail Map

| Function | Baseline component | Rev A rail | Rev B rail | Low-power state |
|---|---|---:|---:|---|
| MCU / BLE | Ezurio BL654 | 3V0_MAIN | 3V3_MAIN | System ON idle; System OFF only for storage |
| Temperature / humidity | Sensirion SHTC3 | 3V0_MAIN | 3V3_MAIN | Explicit sleep command |
| VOC / IAQ / pressure | Bosch BME688 | 3V0_MAIN or VDD_1V8_EVAL | 3V3_MAIN or VDD_1V8_EVAL | BSEC ULP / sensor sleep between heater events |
| Ambient light | Vishay VEML7700 | 3V0_MAIN | 3V3_MAIN | Software shutdown between readings |
| Wi-Fi | Fanstel WM02C | — | 3V3_WIFI_SW | Hard off through TPS22919 |
| Optional 1.8 V sensor | ENS160 or multispectral sensor | Not fitted; populated evaluation rail is reserved for BME688 testing on pre-v1.0 hardware | Future variant may reuse VDD_1V8_EVAL after load review | Converter may be omitted after BME688 rail selection is frozen |
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
| BME688 | BSEC ULP | 90 µA average | 300 s IAQ update interval; specified at VDD ≤ 1.8 V |
| BME688 | Heater operating | 12 mA typical, 13 mA max | Heater target 320 °C; specified at VDD ≤ 1.8 V and 25 °C |
| BME688 | Heater turn-on peak | 17 mA typical, 18 mA max | First milliseconds of hotplate turn-on |
| VEML7700 | Shutdown | 0.5 µA typical | [VEML7700 datasheet](https://www.vishay.com/docs/84286/veml7700.pdf) |
| VEML7700 | Active, PSM disabled | 45 µA for 100 ms | Used to derive 0.57 µA at one sample per minute |
| TPS63900 | Operating quiescent current | 0.075 µA typical | Included in Rev A |
| TPS62840 | Operating quiescent current | 0.060 µA typical | Populated on pre-v1.0 BME688 evaluation hardware; included only in measured 1.8 V cases, not the direct-main-rail planning baseline |
| TPS63802 | Operating quiescent current | 11 µA typical | Included in Rev B |
| BQ24074 | Battery sleep current, USB absent | 4.3 µA typical, 6.5 µA max at stated condition | Conservative 6.5 µA used |
| TPS22919 | Off-state current | 2 nA typical | Wi-Fi module isolated |
| WM02C / nRF7002 | TX | 191 mA at 2.4 GHz; 260 mA at 5 GHz | 260 mA used for rail design |
| WM02C / nRF7002 | RX listen | approximately 56–60 mA | Peak/active state |
| WM02C / nRF7002 | Connected power save | approximately 4 mA planning value | Used only for USB-mode range |
| CR2477 | Nominal rating | 3 V, 1000 mAh, 0.2 mA standard continuous drain | [Panasonic CR2477](https://energy.panasonic.com/na/business/products/lithium/coin-cr-standard/models/CR2477) |

The baseline budgets retain the 90 µA BME688 ULP value as a planning proxy, not as a characterized 3.0 V or 3.3 V value. Bosch specifies that average at VDD ≤ 1.8 V and states that the device is optimized for 1.8 V. The actual main-rail current and the end-to-end benefit of the 1.8 V buck are therefore dominant uncertainties that must be measured with the actual BSEC configuration.

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
| BME688 BSEC ULP planning proxy (specified at VDD ≤ 1.8 V) | 90.00 µA |
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

### 9.3 BME688 1.8 V Sensitivity

The present seven-month design case assumes 90 µA on 3V0_MAIN even though that value is specified only at VDD ≤ 1.8 V. As an illustrative sensitivity case, if the BME688 consumes 90 µA at 1.8 V, both buck stages average 90% efficiency, and the TPS62840 adds 60 nA quiescent current, the modeled Rev A battery-equivalent current falls from approximately 136 µA to approximately 98 µA. Estimated life rises from about 7.0 months to about 9.8 months.

This is a hypothesis, not a prediction. Actual 3.0 V BME688 current, heater behavior, converter efficiency across the pulsed load, and BSEC performance must be measured. The production rail shall be chosen from measured battery-input energy and IAQ behavior.

The Rev A multi-month target is met with the BME688 ULP profile. The BME688 consumes about 73% of the conservative rail budget and is the primary battery-life lever.

### 9.4 Rev A Mode Budget

| System mode | Expected current | Notes |
|---|---:|---|
| Storage, electronics connected | Target ≤3 µA | BL654 System OFF plus sleeping sensors and converter; verify on board |
| Between scheduled events | Approximately 4–6 µA | BL654 System ON idle plus sleeping sensors |
| BLE advertising | Approximately 5–15 mA instantaneous | Average covered by advertising allowance |
| SHTC3/lux sampling | Approximately 4–6 mA while MCU is active | Short duration |
| BME688 heater event | 12 mA typical; 18 mA max turn-on peak, plus MCU | Long enough that the battery, not only a capacitor, must support it |

### 9.5 Rev A Peak-Current Decision

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
- expose battery voltage and 3V0_MAIN test access;
- on pre-v1.0 hardware, populate the 1.8 V evaluation rail and a break-before-make SPDT selector between 3V0_MAIN and 1.8 V for BME688 VDD, with VDDIO fixed to 3V0_MAIN;
- include the shunted current headers, separate voltage headers, and event-marker test points defined in Section 13;
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

## 13. Power Verification and Measurement Access Plan

The PCB shall be intentionally partitioned so current and voltage can be measured at the complete-device input and at the major load branches with a Nordic Power Profiler Kit II (PPK2), source-measure unit, Joulescope, oscilloscope, or equivalent instrument. These provisions are part of the early prototype design, not an afterthought added with cut traces.

Board maturity and feature architecture use separate identifiers: Rev A and Rev B describe product feature sets, while v0.x and v1.x describe PCB maturity. Unless a specific validation need overrides this rule, board versions below v1.0 use removable measurement headers; v1.0 and later use compact production test points after the relevant current measurements and rail choices have been validated.

### 13.1 Required PCB Measurement Architecture

#### Pre-v1.0 series-current headers

- Put a two-pin, 2.54 mm-pitch male pin header in series with the high-side DC feed to each required measurement domain. Label the pins SOURCE and LOAD.
- Fit a standard 2.54 mm removable jumper shunt for normal operation. Removing the shunt shall open only that domain and allow an ammeter to be connected between SOURCE and LOAD with female-ended 2.54 mm DuPont leads, without soldering or cutting a trace.
- Use a compact, low-profile header and shunt whose voltage, current, contact-resistance, and cycle-life ratings cover the domain. The Rev B battery/main/Wi-Fi paths shall be rated for their approximately 300 mA-class peaks.
- Place the disconnect upstream of the DUT local decoupling so series measurement captures the charge drawn by the DUT and its bypass network.
- Keep ground continuous. Do not place the measurement disconnect in the ground return, converter switching node, or a high-di/dt commutation loop.
- Document allowed power-injection states. External power shall never be applied to a downstream rail while its upstream source is connected unless the schematic explicitly supports that condition.

#### Separate voltage headers

- Place a separate two-pin, 2.54 mm-pitch female socket header near each current-measurement domain, with contacts VDD_DUT and GND. VDD_DUT shall be sensed on the load side of the current header so jumper, connector, and test-lead voltage drops are visible.
- Connect voltage instruments with male-ended 2.54 mm DuPont leads. Do not populate male pins at a voltage-measurement position.
- Do not use a combined three-pin VDD_INPUT/VDD_OUTPUT/GND header. Separating the functions prevents a current jumper from being placed between a supply contact and ground.
- Male current headers and female voltage headers are mandatory physical differentiation. A standard jumper shunt can fit only the male current header and therefore cannot short VDD_DUT to GND at the voltage header. Silkscreen shall reinforce, not replace, this mechanical safeguard.
- Place the ground contact physically close to the DUT return and keep the measurement loop short.
- Label every header with the domain and function on both the schematic and silkscreen.

#### v1.0-and-later production access

- After the corresponding power measurements and BME688 rail selection are closed, v1.0 and later hardware may replace the removable current and voltage headers with compact labeled test points.
- Retain at minimum battery/input voltage, main rail, BME688 VDD, ground, SWD, power-control/status, and profile-event test points. Rev B shall also retain USB input and 3V3_WIFI_SW test points.
- Production test points preserve voltage, continuity, control-signal, and event-correlation debugging. Per-branch series-current insertion is no longer guaranteed after the shunted current headers are removed; any domain that still requires production current profiling shall retain an explicit removable link or fixture-accessible disconnect.
- Do not mark the hardware v1.0 until the pre-v1.0 current measurements needed to close the power architecture have been completed.

Minimum pre-v1.0 measurement domains:

| Header / access point | Rev A | Rev B | Purpose |
|---|---:|---:|---|
| BAT_IN | Required | Required | Complete battery-powered device, including regulators |
| USB_IN | — | Required | Complete USB-powered device and charger behavior |
| MAIN_RAIL | Required | Required | Post-converter system load and converter-efficiency comparison |
| MCU_BLE | Required | Required | BL654, external LFXO, and associated local support load |
| BME688_VDD | Required | Required | ULP/heater cycle and 3.0/3.3 V versus 1.8 V comparison |
| SHTC3_VDD | Required | Required | Temperature/humidity event and sleep current |
| VEML7700_VDD | Required | Required | Lux conversion and shutdown current |
| WIFI_SW | — | Required | Wi-Fi inrush, association, TX, and hard-off leakage |

If board area becomes constrained, BAT_IN, MAIN_RAIL, MCU_BLE, BME688_VDD, and Rev B WIFI_SW are the highest-priority individual domains. SHTC3 and VEML7700 may share a secondary sensor-branch header only if each can still be isolated by a removable population option.

#### Rail and signal breakout

On pre-v1.0 hardware, provide the dedicated voltage headers described above for:

- raw battery input and ground;
- 3V0_MAIN or 3V3_MAIN and ground;
- VDD_1V8_EVAL and ground;
- 3V3_WIFI_SW and ground on Rev B.

Also provide labeled test-point access for:

- power-control signals, including the 1.8 V converter enable and Wi-Fi switch enable;
- battery-divider enable and ADC node;
- BQ24074 PGOOD and CHG on Rev B;
- at least two spare/profile GPIOs from the BL654.

Headers and pads shall remain accessible with the normal prototype enclosure open. Test points retained on v1.0 and later hardware shall be compatible with spring probes or grabber leads where board area permits.

#### Time-correlated event markers

Firmware shall drive dedicated PROFILE_EVENT GPIOs around BME688 heater activity, BLE radio windows, sensor conversions, and Rev B Wi-Fi events. Route at least two such GPIOs to test points or a small logic header on pre-v1.0 hardware. PPK2 digital inputs or an oscilloscope can then correlate code state with the current trace. Retain compact profile-event test points on v1.0 and later hardware where practical.

#### BME688 A/B selection

- Populate VDD_1V8_EVAL and one break-before-make micro DIP/slide selector with an SPDT source-selection function on pre-v1.0 evaluation hardware.
- Connect the selector common to BME688 VDD and its throws to the main rail and VDD_1V8_EVAL. A DPDT selector may use its second pole to disable TPS62840 in the main-rail position; otherwise account for the buck's approximately 60 nA quiescent current. Do not implement the selector with two independently operated SPST switches.
- Keep BME688 VDDIO and I²C pull-ups on the main logic rail in both positions.
- Put the BME688_VDD current header downstream of the selector so the same access measures either supply.
- Put the BME688 voltage header on the load side of that current header. Provide additional VDD_1V8_EVAL and main-rail test access to measure converter input/output behavior.
- Change the selector only while the board is unpowered. After power is restored, firmware shall initialize the BME688 normally.
- After the measured comparison closes the choice, v1.0 and later hardware may hardwire the selected BME688 rail and omit the selector and unused evaluation circuitry.

### 13.2 Rev A Measurements

1. Measure electronics-only storage current at 25 °C; pass target ≤3 µA.
2. Measure System ON idle with RTC, full firmware image, and all sensors sleeping; pass target ≤7 µA.
3. Measure charge per three-channel BLE advertising event at the selected interval and TX power.
4. Measure SHTC3 and VEML7700 event charge at their final cadence.
5. Measure one complete BME688 ULP cycle and long-term average with identical BSEC version, configuration, cadence, and environmental exposure at 3.0 V VDD and 1.8 V VDD.
6. For each BME688 supply option, record BME688 branch charge, complete-device battery-input charge, heater peak, main-rail droop, IAQ accuracy status, and stabilization behavior.
7. Capture 3V0_MAIN and raw-cell droop during BME688 heater start and BLE TX.
8. Repeat peak tests with a fresh cell, a partially discharged cell, and an aged/high-resistance cell at low indoor temperature.
9. Run a long-duration coulomb-counting test and compare against the model.

### 13.3 Rev B Measurements

1. Verify battery-only, USB-only with no battery, USB with battery, and source-transition behavior.
2. Verify charger thermal behavior at 400–500 mA charge current.
3. Measure Wi-Fi rail inrush and confirm clean startup through TPS22919.
4. Measure complete Wi-Fi event energy from rail enable through association, upload, acknowledgement, and rail discharge.
5. Test weak-signal and retry cases at 2.4 GHz and 5 GHz.
6. Capture 3V3_WIFI_SW minimum voltage during 260 mA-class TX bursts.
7. Repeat the BME688 3.3 V versus 1.8 V comparison used for Rev A and update the no-Wi-Fi baseline budget from the measured result.
8. Verify that an unpopulated Wi-Fi module does not affect BLE/sensor operation.
9. Confirm battery-life policy using measured event energy and update the cadence table.

### 13.4 Instruments and Capture

The Nordic PPK2 is well matched to this prototype because it supports both source and ampere-meter modes, measures low sleep currents and active peaks across a wide dynamic range, and provides digital inputs for code-synchronized traces. Use source mode at BAT_IN when characterizing the electronics independently of cell variability. Use ampere-meter mode with the actual cell or power path when characterizing battery droop, regulator efficiency, and realistic system behavior.

A single handheld DMM average is not sufficient. Retain raw traces and exported data with firmware commit, board revision, population option, supply voltage, temperature, RF conditions, and BSEC configuration recorded for each run.

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
- [x] No mandatory production 1.8 V domain; populated BME688 1.8 V evaluation path required on pre-v1.0 prototypes.
- [x] Baseline sensor power gating removed.
- [ ] Add a break-before-make SPDT BME688 main-rail/1.8 V selector with VDDIO fixed to the main rail.
- [ ] Add pre-v1.0 whole-device and per-load shunted current headers, separate local voltage headers, rail/signal breakouts, and profile-event GPIO test points.
- [ ] Replace development headers with the defined compact test-point set at v1.0 after validation closes.
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
6. **Sensor load switches remain unjustified, but the BME688 1.8 V question is open for measurement.** Pre-v1.0 boards shall provide solderless selection between main-rail and efficient 1.8 V operation; v1.0 production hardware will follow measured battery-input energy.
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
- [TI TPS62840 datasheet](https://www.ti.com/lit/ds/symlink/tps62840.pdf)
- [TI TPS63802 datasheet](https://www.ti.com/lit/ds/symlink/tps63802.pdf)
- [TI BQ24074 datasheet](https://www.ti.com/lit/ds/symlink/bq24074.pdf)
- [TI TPS22919 datasheet](https://www.ti.com/lit/gpn/TPS22919)
- [Nordic Power Profiler Kit II](https://www.nordicsemi.com/Products/Development-hardware/Power-Profiler-Kit-2)
- [Panasonic CR2477 product data](https://energy.panasonic.com/na/business/products/lithium/coin-cr-standard/models/CR2477)

---

## 17. Revision Notes

| Date | Change |
|---|---|
| 2026-08-12 | Replaced prototype 0 Ω/DNP-header measurement links with normally shunted two-pin 2.54 mm male series-current headers plus separate two-pin 2.54 mm female local VDD/GND voltage headers; specified v0.x header and v1.0+ test-point policy; changed BME688 evaluation to a populated break-before-make SPDT micro selector. |
| 2026-08-04 | Reopened BME688 supply-voltage selection for measured 3.0/3.3 V versus 1.8 V comparison; added prototype current-measurement links, rail/signal breakouts, and event-marker requirements. |
| 2026-08-04 | Completed rail selection, topology and component decisions, duty-cycle assumptions, current budgets, battery-life estimates, peak analysis, schematic requirements, and validation plan. |
