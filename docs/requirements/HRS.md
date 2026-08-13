# MatterSense  
## Hardware Requirements Specification (HRS)

**Document Purpose:**  
This Hardware Requirements Specification (HRS) defines the required hardware capabilities, interfaces, and operating assumptions for the MatterSense device family. It establishes a contract between hardware and firmware development, freezing hardware intent prior to detailed schematic, PCB layout, and firmware implementation.

---

## 1. Scope & Applicability

### 1.1 Covered Revisions

This document applies to the following hardware revisions:

- **MatterSense Rev A**
  - BLE commissioning and Matter-over-Thread connectivity
  - Coin-cell powered
  - Ultra-low-power operation

- **MatterSense Rev B**
  - BLE commissioning with Thread and Wi-Fi capability
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
- Primary power source: one CR2477-class coin-cell battery
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

- Hardware shall measure battery voltage through a normally-off divider connected to an MCU ADC input
- Rev B shall additionally expose charger power-good and charge-status signals to the MCU
- Accuracy shall be sufficient for coarse battery state-of-charge estimation

---

## 3. Voltage Domains & Power Targets

### 3.1 System Voltage

- Primary logic voltage domain expected to be approximately 3 V class
- Pre-v1.0 evaluation hardware shall populate an efficient 1.8 V rail and permit solderless BME688 VDD selection between it and the primary rail through one break-before-make micro switch with an SPDT source-selection function; BME688 VDDIO shall remain on the primary logic rail
- The BME688 supply selector shall be changed only while the board is unpowered and shall not have any allowed state that connects the two source rails together
- After measured validation closes the BME688 rail decision, v1.0 and later hardware may hardwire the selected rail and omit the selector and unused evaluation circuitry
- Voltage regulation must support:
  - Low quiescent current
  - Stable operation across battery discharge range

### 3.2 Power Consumption Targets

**Rev A**
- Deep sleep current target: single-digit microamps or lower
- Active Thread/BLE radio operation optimized for coin-cell operation

**Rev B**
- Hardware shall tolerate high peak current during Wi-Fi transmission
- Power architecture must prevent brownout during RF burst activity

---

## 4. MCU / SoC Requirements

### 4.1 Required MCU Capabilities

The primary MCU / SoC shall provide:

- Integrated 2.4 GHz radio supporting BLE
- Integrated IEEE 802.15.4 radio supporting Thread
- Sufficient internal flash and RAM for Matter-capable firmware
- Hardware cryptographic acceleration
- Secure or unique device identity storage (e.g., device ID, keys)

### 4.2 Required Peripheral Interfaces

The MCU shall provide, at minimum:

- I²C master interface for sensors
- Dedicated QSPI interface for external NOR flash
- Standard SPI host interface for the Wi-Fi module (Rev B)
- ADC inputs for battery monitoring and optional sensors
- GPIOs for LEDs, buttons, and power control
- SWD or equivalent debug/programming interface
- NFC interface pins (Rev B)

### 4.3 External Nonvolatile Memory

- Both revisions shall populate one 64-Mbit (8 MiB) low-power QSPI NOR flash device.
- The schematic baseline shall use Macronix **MX25R6435FZNIL0**, an active 1.65–3.6 V, low-power-default device in an 8-WSON (6 × 5 mm) package.
- The flash shall be powered from the unswitched primary logic rail and placed in deep-power-down mode between accesses; a separate load switch is not required.
- The QSPI bus shall be dedicated to external flash. Rev B shall use a separate SPIM instance for the WM02C Wi-Fi host interface.
- Reserve at least 2 MiB for MCUboot/Matter OTA staging until the signed production image size and partition layout are measured. The remaining capacity may support settings, crash records, and a wear-aware circular sensor-history buffer.
- Flash writes shall be buffered and aligned to page/erase boundaries where practical. Firmware shall not erase a sector for every sensor sample.
- The active firmware image shall remain in internal MCU flash; external flash is a staging and data-storage device, not a sole boot dependency.

---

## 5. Sensor & Measurement Requirements

### 5.1 Required Sensors

Both hardware revisions shall support the following sensor classes:

- Temperature and Humidity
- Volatile Organic Compounds (VOC) / Equivalent CO<sub>2</sub> (eCO<sub>2</sub>) and barometric pressure from the baseline BME688
- Ambient Light (Lux)

### 5.2 Optional Sensors

Provision may be made for the following optional sensors:

- Multispectral light sensing on a future Rev B variant
- Sound level (dB or microphone-based) on a future externally powered variant

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
- BLE shall provide commissioning and may provide development/service functions. Continuous post-commissioning advertising is not required unless a defined feature uses it.

### 6.2 Wi-Fi Requirements (Rev B)

- Wi-Fi shall support:
  - 2.4 GHz and 5 GHz operation
  - Matter-over-Wi-Fi operation through the WM02C companion
  - Secure onboarding and encrypted communication
- Hardware shall allow Wi-Fi to be fully powered down in the Thread build or when Wi-Fi is intentionally unavailable
- A Matter-over-Wi-Fi build shall use a supported associated power-save mode during battery operation; it shall not treat multi-hour hard-off intervals as normal Matter reachability

### 6.3 NFC Requirements (Rev B)

- NFC shall support out-of-band (OOB) pairing
- NFC antenna connection must be exposed at the PCB level
- Firmware assumes NFC is primarily active during onboarding

### 6.4 Thread and Zigbee

- Rev A shall use Matter-over-Thread as its normal operational transport and shall operate as a low-power Thread Sleepy End Device or Matter Intermittently Connected Device.
- Rev B shall retain Thread capability as a separate firmware/build option when Wi-Fi is unpopulated. Runtime switching between a commissioned Thread network and Wi-Fi is not required.
- Rev A Thread operation shall use the BL654/nRF52840 IEEE 802.15.4 radio; no additional Thread radio is required.
- The selected Rev B host shall retain an IEEE 802.15.4 radio for the Matter-over-Thread build/population option.
- Zigbee remains a firmware-driven future possibility and is subject to stack and certification requirements.

### 6.5 Antenna Strategy

- Rev A BLE/Thread shall use the integrated trace antenna in BL654 orderable part 451-00001.
- Rev B BLE/Thread shall use the integrated antenna of the selected nRF5340-class host module, or a separately validated antenna if a discrete host is selected.
- Rev B Wi-Fi shall use the integrated chip antenna in the WM02C.
- Both module antennas shall follow their vendor edge-placement, copper-keepout, enclosure-clearance, and ground requirements.
- Rev B shall provide the nRF52840 NFC antenna connection and matching provisions. The exact PCB loop or external-antenna implementation remains a schematic/layout selection.

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
- Development-only headers may be omitted from v1.0 and later production assemblies after their associated validation requirements are complete

### 8.3 Power-Profiling Access

- Rev A and Rev B identify feature architectures; v0.x and v1.x identify PCB maturity
- Board versions below v1.0 shall provide the full removable power-measurement header set
- Pre-v1.0 PCBs shall permit current measurement of the complete battery-powered device, including regulator losses
- Rev B shall additionally permit measurement at USB input and the switched Wi-Fi rail
- Major load branches shall be individually measurable at minimum for:
  - MCU / Thread / BLE module
  - External QSPI flash
  - BME688 VDD
  - SHTC3 VDD
  - VEML7700 VDD
  - Wi-Fi module on Rev B
- Each pre-v1.0 current-measurement domain shall use a two-pin 2.54 mm-pitch male high-side pin header labeled SOURCE and LOAD with a standard removable jumper shunt fitted for normal operation
- Removing the shunt shall open only that domain and allow a Power Profiler Kit II, source-measure unit, Joulescope, or equivalent instrument to be inserted in series with female-ended 2.54 mm DuPont leads, without soldering or cutting PCB traces
- Ground shall remain continuous during high-side current measurement
- Each current domain shall have a separate two-pin 2.54 mm-pitch female socket header for VDD_DUT/GND near the DUT; VDD_DUT shall connect on the load side of the current header, and voltage instruments shall connect with male-ended 2.54 mm DuPont leads
- A combined VDD_INPUT/VDD_OUTPUT/GND three-pin header shall not be used
- Male pins shall not be populated at voltage-measurement positions
- Male current headers and female voltage headers shall provide mandatory physical differentiation so a current jumper shunt cannot be installed across VDD and ground; silkscreen shall reinforce this distinction
- Current headers, shunts, and traces shall be rated for the applicable peak current and acceptable contact voltage drop
- The BME688 current header shall be downstream of its break-before-make main-rail/1.8 V selector so either supply option uses the same measurement access
- The BME688 voltage header shall sense BME688 VDD and local ground downstream of the current header
- At least two MCU GPIOs shall be exposed as profile-event markers for time-correlating sensor, heater, Thread/BLE, flash, and Wi-Fi activity with current traces
- Schematic, silkscreen, and assembly notes shall identify header functions and prevent invalid external-injection or selector states that short or back-power power domains
- After validation, v1.0 and later hardware may replace removable development headers with compact labeled test points
- v1.0 and later test points shall retain at minimum battery/input voltage, main rail, external-flash VDD, BME688 VDD, ground, SWD, key power-control/status signals, and profile-event GPIOs; Rev B shall also retain USB input and the switched Wi-Fi rail
- Compact production test points preserve voltage and signal debugging but do not inherently preserve per-branch series-current insertion; any domain that still requires production current profiling shall retain a removable link or fixture-accessible disconnect
- Hardware shall not advance to v1.0 until the pre-v1.0 measurements needed to close the power architecture have been completed

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

## 10. Remaining Selections

### 10.1 Rev B Architecture Blocker

Rev A has the selected hardware needed to start schematic capture. Rev B does not:
Nordic's current supported Matter-over-Wi-Fi platforms pair nRF7002 with an
nRF5340-class host, while BL654/nRF52840 is supported for Matter-over-Thread but is
not listed as a Matter-over-Wi-Fi host. Before capturing the Rev B MCU/radio portion,
the project shall either:

- select an nRF5340-class MCU/module with the required BLE, Thread, NFC, QSPI, SPI,
  ADC, GPIO, antenna, memory, and certification characteristics; or
- formally change the Rev B product requirement to retain Matter-over-Thread and use
  Wi-Fi only for a non-Matter data path.

The WM02C, external flash, sensors, and power-path selections can be retained, but
their pin mapping and the Rev B current model shall be revalidated against the chosen
host.

### 10.2 Schematic and Layout Selections

The following non-architectural selections are intentionally completed during
schematic capture or layout. They do not block Rev A schematic capture and will not
block Rev B after the host decision is closed:

- Exact converter inductors, capacitors, feedback/current-limit components, and bias-effective capacitance
- Exact CR2477 holder and protected 2000 mAh LiPo pack, connector, polarity, retention, and NTC implementation
- Exact USB-C receptacle, CC resistors, ESD/TVS and input-protection parts for Rev B
- Exact 32.768 kHz crystal and load capacitors
- Exact BME688 break-before-make selector, current/voltage headers, shunts, and v1.0 test-pad geometry
- Exact SWD/bed-of-nails pad geometry
- Rev B NFC antenna and matching implementation
- Exact status LED and optional user-button circuitry
- Exact models for future non-baseline sensors
- Final enclosure design

These items will be resolved during schematic, layout, and validation phases.
