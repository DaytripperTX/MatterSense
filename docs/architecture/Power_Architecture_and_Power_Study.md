# MatterSense – Power Architecture and Power Study

## 1. Purpose

This document defines the power architecture for MatterSense Rev A and Rev B, including:
- System power rails and their roles
- Component-to-rail voltage mapping
- Voltage compatibility across all selected components
- Foundation for power consumption analysis and battery life estimation

This document will also include a detailed power study (to be completed after rail mapping is finalized).

---

## 2. Scope

- Rev A: BLE-only, coin cell powered
- Rev B: BLE + Wi-Fi, rechargeable battery and/or USB-powered
- Covers:
  - Power rail definition
  - Voltage selection
  - Component compatibility
  - Power budgeting framework

Does NOT yet finalize:
- Regulator IC selection
- Battery selection
- Exact efficiency calculations

---

## 3. Design Inputs and Assumptions

### 3.1 Tentative Power Rails

| Rail Name | Nominal Voltage | Description |
|----------|----------------|-------------|
| 3V3_MAIN | 3.3V | Primary system rail (MCU + most sensors) |
| 1V8 | 1.8V | Low-voltage rail (sensor core / digital domains) |
| 3V3_WIFI_SW | 3.3V | Load-switched rail for Wi-Fi subsystem (Rev B only) |

### 3.2 Key Assumptions

- BLE must remain available in low-power states (Rev A and Rev B battery mode)
- Wi-Fi is **disabled in battery mode except when needed**
- Wi-Fi must be fully power-gated in low-power states
- Sensors will be duty-cycled where possible
- Rail definitions may change after power study

---

## 4. Top-Level Power Architecture

### 4.1 Power Sources

| Source | Rev | Notes |
|--------|-----|------|
| Coin Cell Battery | Rev A | Primary power source |
| LiPo Battery | Rev B | Rechargeable power source |
| USB-C | Rev B | External power + charging |

### 4.2 Power Domains

| Domain | Description |
|--------|------------|
| Always-On Domain | BLE MCU + essential sensors |
| Sensor Domain | Duty-cycled sensors |
| Wi-Fi Domain | High-power, load-switched subsystem |

### 4.3 Rail Interaction Notes

- 3V3_MAIN is the primary logic domain
- 1V8 is used where required by sensor cores or interfaces
- 3V3_WIFI_SW must be independently switchable
- Level shifting may be required between 1.8V and 3.3V domains (TBD)

---

## 5. Power Rail Map

### 5.1 Rail Definitions

| Rail | Nominal Voltage | Present In | Switched | Loads | Notes |
|------|----------------|------------|----------|-------|------|
| 3V3_MAIN | 3.3V | Rev A / Rev B | No | MCU, sensors, logic | Primary rail |
| 1V8 | 1.8V | Rev A / Rev B | No (TBD) | Sensor cores, possible digital interfaces | Required by some sensors |
| 3V3_WIFI_SW | 3.3V | Rev B only | Yes | Wi-Fi subsystem | Power-gated for battery savings |

---

### 5.2 Component-to-Rail Mapping

| Function | Component | Operating Voltage Range | I/O / Secondary Supply | Selected Voltage | Assigned Rail | Notes |
|----------|----------|-------------------------|------------------------|------------------|---------------|-------|
| BLE MCU / Radio | [Ezurio BL654](https://www.ezurio.com/documentation/datasheet-bl654) | 1.7V – 3.6V (VDD), 2.5V – 5.5V (VDD_HV) | — | 3.3V | 3V3_MAIN | — |
| BLE MCU / Radio (alternative) | [u-blox BMD-340-A-R](https://content.u-blox.com/sites/default/files/BMD-340_DataSheet_UBX-19033353.pdf) | 1.7V – 3.6V | — | 3.3V | 3V3_MAIN | Lower cost alternative |
| Temp / Humidity | [Sensirion SHTC3](https://sensirion.com/resource/datasheet/shtc3) | 1.62V – 3.6V | — | 3.3V | 3V3_MAIN | — |
| Temp / Humidity (alternative) | [Sensirion SHT40](https://sensirion.com/products/catalog/SHT40) | 1.08V – 3.6V | — | 3.3V | 3V3_MAIN | Lower power alternative |
| VOC / IAQ | [Bosch BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | VDD: 1.71V – 3.6V | Optional VDDIO: 1.2V – 3.6V | 3.3V | 3V3_MAIN | Also supports barometric sensing |
| VOC / IAQ (alternative) | [ScioSense ENS160](https://www.sciosense.com/wp-content/uploads/2023/12/ENS160-Datasheet.pdf) | VDD: 1.71V – 1.98V | VDDIO: 1.71V – 3.6V | 1.8V | 1V8 | Requires 1.8V core rail |
| Ambient Light (Lux) | [Vishay VEML7700](https://www.vishay.com/docs/84286/veml7700.pdf) | 2.5V – 3.6V | — | 3.3V | 3V3_MAIN | — |
| Ambient Light (alternative) | [ams OSRAM AS7341](https://look.ams-osram.com/m/24266a3e584de4db/original/AS7341-DS000504.pdf) | ~1.7V – 2.0V | — | 1.8V | 1V8 | Requires 1.8V rail |
| Ambient Light (alternative) | [ams OSRAM TCS3448](https://look.ams-osram.com/m/1c24b057e65ee61e/original/TCS3448-14-Channel-multi-spectral-sensor.pdf) | 1.7V – 1.98V | — | 1.8V | 1V8 | Requires 1.8V rail |
| Barometric Pressure (optional) | [Bosch BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | VDD: 1.71V – 3.6V | Optional VDDIO: 1.2V – 3.6V | 3.3V | 3V3_MAIN | No additional sensor required |
| Wi-Fi Companion (Rev B baseline) | [Fanstel WM02C](https://static1.squarespace.com/static/561459a2e4b0b39f5cefa12e/t/672e447ee28d49742366de31/1731085441199/WM02C%2BProduct%2BSpecifications.pdf) | 2.9V – 4.5V | — | 3.3V | 3V3_WIFI_SW | Must be load-switched |
| Wi-Fi Companion (alternative) | [Nordic nRF7002](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/6470/NRF7002-QFAA-R.pdf) | 2.9V – 4.5V | — | 3.3V | 3V3_WIFI_SW | Must be load-switched |
| Optional Sound / dB Front End | [Infineon IM69D130](https://www.infineon.com/dgdl/Infineon-IM69D130-DataSheet-v01_00-EN.pdf?fileId=5546d462602a9dc801607a0e46511a2e) | 1.62V – 3.6V | — | 3.3V | 3V3_MAIN | — |
| Optional Sound / dB Front End (alternative) | [ST MP34DT06JTR](https://www.st.com/resource/en/datasheet/mp34dt06j.pdf) | 1.6V – 3.6V | — | 3.3V | 3V3_MAIN | — |


---

### 5.3 Rail Dependency Summary

- No current baseline component strictly requires a 1.8V rail
- The primary components that drive consideration of a 1.8V rail are the alternative multispectral lux sensors:
  - [ams OSRAM AS7341](https://look.ams-osram.com/m/24266a3e584de4db/original/AS7341-DS000504.pdf)
  - [ams OSRAM TCS3448](https://look.ams-osram.com/m/1c24b057e65ee61e/original/TCS3448-14-Channel-multi-spectral-sensor.pdf)
- The Wi-Fi subsystem requires a higher-voltage rail and cannot be operated from 1.8V
- Because the design includes two I²C buses, it is possible to support both 1.8V and 3.3V I²C domains without level shifting, provided all devices on a given bus share the same voltage domain
- 3.3V remains the default system assumption for now due to simplicity and broad compatibility
- Consideration of alternate rail voltages, including 3.0V main rail operation and optional 1.8V subdomains, will be revisited during the power study

---

## 6. Power Study Method

### 6.1 Operating Modes

#### 6.1.1 Component Operating Modes

Define the relevant modes used in the power study for each component class.

- **MCU / BLE**
  - Sleep / System Off
  - Idle / Standby
  - MCU Active
  - BLE RX
  - BLE TX
  - BLE Advertising
  - BLE Connected Idle

- **Wi-Fi**
  - Off
  - Startup / Enable
  - Idle / Associated Idle
  - RX
  - TX

- **Environmental Sensors**
  - Sleep / Standby
  - Measurement / Conversion
  - Warm-up / Heater Stabilization (if applicable)

- **Microphone / Sound Front End**
  - Off / Sleep
  - Active Sampling

#### 6.1.2 System Operating Modes

Define the higher-level system modes that combine component modes.

- Ship / Storage
- Deep Sleep
- BLE Advertising
- BLE Connected Idle
- Periodic Sensor Sampling Event
- Wi-Fi Wake + Upload (Rev B)
- USB-Powered Always-Reachable Mode (Rev B)

### 6.2 Component Electrical Characteristics

This table summarizes the electrical characteristics of each component at the selected operating voltage and any alternate voltages considered during the power study.

| Function | Component | Selected Voltage | Alternate Voltage(s) Considered | Sleep / Standby Current | Idle Current | Active / Measurement Current | Special Mode Current(s) | Notes |
|---------|----------|------------------|---------------------------------|-------------------------|-------------|-------------------------------|-------------------------|------|
| BLE MCU / Radio | [Ezurio BL654](https://www.ezurio.com/documentation/datasheet-bl654) | 3.3V | 3.0V | 0.4 µA | 3.1 µA | — | BLE RX: 4.6 mA; BLE TX (0 dBm): 4.8 mA | |
| Temp / Humidity | [Sensirion SHTC3](https://sensirion.com/resource/datasheet/shtc3) | 3.3V | 1.8V, 3.0V | 0.3 µA | 45 µA | 430 µA (normal measurement) | Low-power measurement: 270 µA | Avg. current much lower |
| Temp / Humidity (alternative) | [Sensirion SHT40](https://sensirion.com/media/documents/33FD6951/6555C40E/Sensirion_Datasheet_SHT4x.pdf) | 3.3V | 1.8V, 3.0V | — | 80 nA | 320 µA | | Avg. current much lower; lower power alt |
| VOC / IAQ | [Bosch BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | 3.3V | 1.8V, 3.0V | 0.15 µA | 0.29 µA | 3.96 mA (standard gas scan) | ULP: 90 µA; LP: 0.9 mA |  Optimized for 1.8V operation |
| VOC / IAQ (alternative) | [ScioSense ENS160](https://www.sciosense.com/wp-content/uploads/2023/12/ENS160-Datasheet.pdf) | 1.8V | VDDIO: 1.8V or 3.3V | 0.01 mA  | 2–2.5 mA | 29 mA | Peak: 79 mA (<5 ms) | Requires 1.8V core rail |
| Ambient Light (Lux) | [Vishay VEML7700](https://www.vishay.com/docs/84286/veml7700.pdf) | 3.3V | 3.0V | 0.5 µA | — | 45 µA | 2 µA (Power Saving Mode) | |
| Ambient Light (alternative) | [ams OSRAM AS7341](https://look.ams-osram.com/m/24266a3e584de4db/original/AS7341-DS000504.pdf) | 1.8V | — | TBD | TBD | TBD | | Requires 1.8V rail |
| Ambient Light (alternative) | [ams OSRAM TCS3448](https://look.ams-osram.com/m/1c24b057e65ee61e/original/TCS3448-14-Channel-multi-spectral-sensor.pdf) | 1.8V | — | TBD | TBD | TBD | | Requires 1.8V rail |
| Barometric Pressure (optional) | [Bosch BME688](https://www.bosch-sensortec.com/media/boschsensortec/downloads/datasheets/bst-bme688-ds000.pdf) | 3.3V | 1.8V, 3.0V | TBD | — | TBD | | |
| Wi-Fi Companion | [Fanstel WM02C](https://static1.squarespace.com/static/561459a2e4b0b39f5cefa12e/t/672e447ee28d49742366de31/1731085441199/WM02C%2BProduct%2BSpecifications.pdf) | 3.3V | — | TBD | TBD | TBD | RX: TBD; TX: TBD | Rev B only |
| Wi-Fi Companion (alternative) | [Nordic nRF7002](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/6470/NRF7002-QFAA-R.pdf) | 3.3V | — | TBD | TBD | TBD | RX: TBD; TX: TBD | |
| Optional Sound / dB Front End | [Infineon IM69D130](https://www.infineon.com/dgdl/Infineon-IM69D130-DataSheet-v01_00-EN.pdf?fileId=5546d462602a9dc801607a0e46511a2e) | 3.3V | 1.8V, 3.0V | TBD | — | TBD | | |
| Optional Sound / dB Front End (alternative) | [ST MP34DT06JTR](https://www.st.com/resource/en/datasheet/mp34dt06j.pdf) | 3.3V | 1.8V, 3.0V | TBD | — | TBD | | |

Unless otherwise noted, current values represent **typical operating currents** taken from component datasheets under nominal conditions. Actual system consumption may vary depending on duty cycle, environmental conditions, and regulator efficiency.

### 6.3 Duty Cycle and Usage Assumptions

This section defines:
- sensor sampling interval
- BLE advertising / connection timing
- Wi-Fi upload interval
- active duration per event
- regulator efficiency assumptions
- battery usable capacity assumptions

**ENS160 Warm-Up Consideration**

The ENS160 requires approximately **3 minutes of warm-up time** after entering STANDARD sensing mode before measurements are considered reliable. This significantly increases the energy cost of each sensing cycle and makes the device poorly suited to aggressive duty cycling. As a result, it is less favorable for low-power battery operation (Rev A) compared to alternatives such as the BME688.

### 6.4 System Power Budget

Combine per-component electrical data with system operating modes and duty cycle assumptions to estimate:
- average current
- average power
- peak current
- battery life

### 6.5 Rail Scenario Comparison

Compare candidate rail architectures such as:
- 3.3V main rail
- 3.0V main rail
- optional 1.8V sensor subdomain

---

## 7. Power Study Results (TBD)

### 7.1 Rev A Current Budget
### 7.2 Rev B Battery Mode
### 7.3 Rev B USB Mode
### 7.4 Peak Current Analysis
### 7.5 Battery Life Estimates

---

## 8. Preliminary Observations

- A 1.8V rail is required due to ENS160
- 3.3V remains the simplest system-wide logic voltage
- A load-switched Wi-Fi rail is critical for Rev B battery life
- Most sensors can likely remain on 3.3V, minimizing complexity

---

## 9. Next Steps

1. Fill in missing component voltages from hardware selection doc
2. Validate rail compatibility across all components
3. Confirm whether 3.3V vs 3.0V is optimal
4. Begin detailed power study calculations
5. Select regulator topology (buck / boost / buck-boost)

---

## 10. Appendix

- Datasheets (to be linked)
- Assumptions log
- Revision notes
