# MatterSense  
## Hardware Requirements Specification (HRS)

**Document Purpose:**  
This Hardware Requirements Specification (HRS) defines the required hardware capabilities, interfaces, and operating assumptions for the MatterSense device family. It establishes a contract between hardware and firmware development, freezing hardware intent prior to detailed schematic, PCB layout, and firmware implementation.

---

## 1. Scope & Applicability

### 1.1 Covered Revisions

This document applies to the following hardware revisions:

- **MatterSense Rev A**
  - BLE-only connectivity
  - Coin-cell powered
  - Ultra-low-power operation

- **MatterSense Rev B**
  - BLE + Wi-Fi connectivity
  - Rechargeable LiPo battery
  - USB-C power and charging support
  - NFC-assisted onboarding

### 1.2 Out of Scope

This HRS does **not** define:

- Electrical schematics
- Component values or final BOM
- PCB layout or antenna tuning
- Enclosure or industrial design

---

## 2. Power Architecture Requirements

### 2.1 Power Sources

**Rev A**
- Primary power source: one or more coin-cell batteries
- System shall operate across the full usable coin-cell voltage range
- No charging circuitry required

**Rev B**
- Primary power source: single-cell LiPo battery
- Secondary power source: USB-C (5 V input)
- System shall support operation while USB-powered with or without battery present

### 2.2 Power Path Behavior (Rev B)

- Hardware shall support:
  - Battery charging from USB-C
  - Seamless power-path management between USB and battery
  - Safe charge termination and protection
- Firmware shall not be required to directly control analog charging behavior

### 2.3 MCU Power Management Responsibilities

- The MCU shall be capable of:
  - Enabling and disabling major power consumers that require hardware control, including Wi-Fi, indicators, and optional evaluation rails
  - Placing baseline sensors into their specified sleep or shutdown modes
  - Entering deep sleep or system-off modes
  - Waking via timer, GPIO, or radio events

### 2.4 Battery Measurement

- Hardware shall expose a means to measure battery voltage
- Measurement may be via:
  - MCU ADC input
  - Power management IC reporting
- Accuracy shall be sufficient for coarse battery state-of-charge estimation

---

## 3. Voltage Domains & Power Targets

### 3.1 System Voltage

- Primary logic voltage domain expected to be approximately 3 V class
- Prototype hardware shall permit BME688 VDD operation from either the primary rail or an efficient 1.8 V evaluation rail while keeping BME688 VDDIO on the primary logic rail
- Voltage regulation must support:
  - Low quiescent current
  - Stable operation across battery discharge range

### 3.2 Power Consumption Targets

**Rev A**
- Deep sleep current target: single-digit microamps or lower
- Active BLE transmission optimized for coin-cell operation

**Rev B**
- Hardware shall tolerate high peak current during Wi-Fi transmission
- Power architecture must prevent brownout during RF burst activity

---

## 4. MCU / SoC Requirements

### 4.1 Required MCU Capabilities

The primary MCU / SoC shall provide:

- Integrated 2.4 GHz radio supporting BLE
- Sufficient Flash and RAM for Matter-capable firmware
- Hardware cryptographic acceleration
- Secure or unique device identity storage (e.g., device ID, keys)

### 4.2 Required Peripheral Interfaces

The MCU shall provide, at minimum:

- I²C master interface for sensors
- SPI or SDIO interface for Wi-Fi module (Rev B)
- ADC inputs for battery monitoring and optional sensors
- GPIOs for LEDs, buttons, and power control
- SWD or equivalent debug/programming interface
- NFC interface pins (Rev B)

---

## 5. Sensor & Measurement Requirements

### 5.1 Required Sensors

Both hardware revisions shall support the following sensor classes:

- Temperature and Humidity
- Volatile Organic Compounds (VOC) / Equivalent CO<sub>2</sub> (eCO<sub>2</sub>)
- Ambient Light (Lux)

### 5.2 Optional Sensors

Provision may be made for the following optional sensors:

- Barometric pressure
- Sound level (dB or microphone-based)

### 5.3 Sensor Interfaces

- Primary sensor interface: I²C
- Optional sensors may use:
  - ADC inputs
  - GPIO-based digital interfaces

### 5.4 Sampling Assumptions

- Sensors are sampled intermittently, not continuously
- Baseline sensors remain powered and use their specified sleep or shutdown modes between events; routine power-gating is not assumed
- Firmware shall preserve BME688/BSEC history and stabilization behavior across normal low-power operation
- Prototype firmware and hardware shall support identical BME688 ULP profiles at main-rail VDD and 1.8 V VDD for energy comparison

---

## 6. RF & Connectivity Requirements

### 6.1 BLE Requirements (Rev A & Rev B)

- BLE radio shall support:
  - Advertising and connection roles
  - Low-power operation suitable for battery-powered devices
- BLE shall remain available during normal device operation modes

### 6.2 Wi-Fi Requirements (Rev B)

- Wi-Fi shall support:
  - 2.4 GHz operation
  - Secure onboarding and encrypted communication
- Hardware shall allow Wi-Fi to be fully powered down when not in use

### 6.3 NFC Requirements (Rev B)

- NFC shall support out-of-band (OOB) pairing
- NFC antenna connection must be exposed at the PCB level
- Firmware assumes NFC is primarily active during onboarding

### 6.4 Thread / Zigbee Forward Compatibility

- Hardware shall not preclude future support for Thread or Zigbee
- The primary MCU shall be capable of supporting Thread and Zigbee via firmware
- No dedicated Thread or Zigbee hardware is required at this stage
- Any future enablement of Thread or Zigbee is expected to be:
  - Firmware-driven
  - Subject to stack selection and certification requirements

### 6.5 Antenna Strategy

- RF shall use:
  - Pre-certified RF modules where practical
  - External or PCB antennas consistent with regulatory goals
- Final antenna tuning and certification strategy are deferred beyond this HRS

---

## 7. User I/O Requirements

### 7.1 Visual Indicators

- At least one user-visible LED shall be provided
- LED shall be controllable via GPIO
- Firmware shall support status-indication patterns

### 7.2 User Input

- Optional pushbutton input shall be supported
- Button may be used for:
  - User reset
  - Pairing initiation
  - Factory reset via long-press

### 7.3 Reset Behavior

- Hardware reset capability is required
- Reset may be triggered via:
  - Dedicated reset pin
  - Power cycling
  - Button-based reset logic

---

## 8. Programming, Debug & Test Requirements

### 8.1 Debug Access

- SWD or equivalent debug interface is required
- Interface shall be accessible during:
  - Development
  - Manufacturing test
- Optional (DNP) I²C breakout header footprint (SCL/SDA/GND/VDD) to support external sensor evaluation and debug during development

### 8.2 Manufacturing Test

- PCB shall expose test points suitable for bed-of-nails probing
- Test access should include:
  - Power
  - Ground
  - Programming interface
  - Key GPIO and power-control/status signals
- Development headers may be DNP in production assemblies

### 8.3 Power-Profiling Access

- Prototype PCBs shall permit current measurement of the complete battery-powered device, including its regulator losses
- Rev B shall additionally permit measurement at USB input and the switched Wi-Fi rail
- Major load branches shall be individually measurable at minimum for:
  - MCU / BLE module
  - BME688 VDD
  - SHTC3 VDD
  - VEML7700 VDD
  - Wi-Fi module on Rev B
- Each measurement domain shall use a normally populated 0 Ω high-side link or equivalent removable connection with paired test pads and a DNP two-pin header footprint
- Removing a link shall allow a Power Profiler Kit II, source-measure unit, Joulescope, or equivalent instrument to be inserted in series without cutting PCB traces
- Ground shall remain continuous during high-side current measurement
- Raw battery/input, regulated main rail, 1.8 V evaluation rail, Rev B Wi-Fi rail, and nearby ground shall be exposed at labeled test points or DNP development headers
- At least two MCU GPIOs shall be exposed as profile-event markers for time-correlating sensor, heater, BLE, and Wi-Fi activity with current traces
- The BME688 current-measurement link shall be downstream of its main-rail/1.8 V source selector so either supply option can be measured through the same access point
- Schematic and assembly notes shall prevent simultaneous population or external injection states that short or back-power power domains
- Production builds may omit development headers and use direct 0 Ω links after validation is complete

### 8.4 Factory Programming Assumptions

- Devices will be programmed prior to final enclosure assembly
- Hardware shall support reliable and repeatable mass-programming workflows

---

## 9. Environmental & Mechanical Assumptions

### 9.1 Operating Environment

- Indoor use only
- No exposure to weather, UV, or liquid ingress

### 9.2 Temperature Range

- Designed for typical indoor ambient temperatures
- Consumer-grade temperature range is acceptable

### 9.3 Size Constraints

- Target device footprint approximately ≤ 2 × 2 inches
- No strict thickness requirement defined at the HRS stage

---

## 10. Open Items / TBD

The following items are intentionally deferred:

- Final component selection and vendor choices
- Final antenna type and tuning
- Exact sensor models for optional sensors
- Final enclosure design
- Final battery capacity selection for Rev B

These items will be resolved during schematic, layout, and validation phases.

---

## 11. Approval & Change Control

Changes to this HRS after approval may require:

- Firmware impact review
- Hardware architecture re-evaluation
- Formal revision tracking and documentation updates
