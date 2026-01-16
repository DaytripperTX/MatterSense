# System Architecture Document  
**Environmental Sensor Node (Rev A & Rev B)**

---

## 1. Purpose

This document defines the **high-level system architecture** for the Environmental Sensor Node project. It describes the major functional blocks, their responsibilities, and how they interact electrically, logically, and wirelessly.

The architecture is designed to:
- Support **low-power battery operation**
- Scale from **BLE-only (Rev A)** to **BLE + Wi-Fi + USB-powered (Rev B)**
- Enable future compatibility with **Matter and common smart-home ecosystems**
- Be suitable for **volume manufacturing and test**

No specific component part numbers are defined in this document.

---

## 2. System Overview

The device is a compact, battery-powered environmental sensor node that periodically samples environmental data and exposes it wirelessly to a smart-home ecosystem.

Two hardware revisions are planned:

- **Rev A**  
  Ultra-low-power BLE device optimized for coin-cell operation.

- **Rev B**  
  Feature-expanded device with Wi-Fi connectivity, USB-C power, rechargeable battery support, and NFC-based onboarding.

Both revisions share a common architectural philosophy and firmware model.

---

## 3. Rev A Architecture (BLE, Coin Cell)

### 3.1 High-Level Block Diagram

```
                ┌────────────────────────────────────────────┐
                │              Mobile / Hub / App            │
                │   (Matter controller / BLE central role)   │
                └───────────────────────┬────────────────────┘
                                        │ BLE (advertising + GATT)
                                        ▼
┌──────────────────────────────────────────────────────────────────────────────────┐
│                                  Rev A Device                                    │
│                                                                                  │
│   ┌────────────────────────┐                    ┌─────────────────────────────┐  │
│   │ Sensors / Measurements │                    │                             │  │    
│   | - T/H Sensor           |                    │                             │  │
│   │ - VOC / Gas            │                    │  BLE SoC / MCU              │  │
│   | - Lux Sensor           |                    │  - BLE radio + stack        │  │
│   │ - (Optional):          │◄── I²C / GPIO ────►│  - App logic / scheduling   │  │
│   │  - Pressure Sensor     │       / ADC        |  - Sensor polling           |  |
|   |  - dB / Sound          |                    |  - Power state control      |  |
|   |  - Battery Sense       |                    │  - Secure storage / IDs     │  │
│   └────────────────────────┘          ┌────────►│                             │  │
│                                       |         │                             │  │
│                                       |         └───────────┬───────────────┬─┘  │
│                                       |                     │               |    │
│                                       |         ┌───────────▼───────────┐   |    │
│                                       |         │  User I/O (optional)  │   |    │
│                                       |         │  - Status LED(s)      │   |    │
│                                       |         │  - Button / Reset     │   |    │
│                                       |         └───────────────────────┘   |    │
│                                       |                                     |    │
│   ┌─────────────────────────┐         |                                     |    │
│   │ Coin Cell Power         │         |                                     |    │
│   │ - Battery holder        │         |                                     |    │
│   │ - Protection (optional) │         |                                     |    │
│   │ - Load switch (opt)     │         |                                     |    │
│   └───────────┬─────────────┘         |                                     |    │
│               │                       |                                     |    │
│   ┌───────────▼──────────────┐        │                                     |    |
│   │ Power Mgm                │◄───────┘                                     |    |
│   │ - Regulator              │                                              |    |
│   │ - Fuel gauge (optional)  │                                              |    |
│   │   (or VBAT ADC)          │                                              |    |
│   └──────────────────────────┘                                              |    │
│                                                                             |    │
│              ┌───────────────────────────────────────────────────┐          |    │
│              │ Programming / Debug (development + test)          │          |    │
│              │ - SWD pads / minimal footprint access             │◄─────────┘    │
│              │ - Test points (power, reset, key signals)         │               │
│              └───────────────────────────────────────────────────┘               │
└──────────────────────────────────────────────────────────────────────────────────┘
```
---

### 3.2 Functional Description

**BLE SoC / MCU**
- Central controller for all system functions
- Handles sensor polling, data aggregation, and power-state transitions
- Manages BLE advertising, connections, and secure communication
- Enters deep sleep between scheduled events

**Sensors**
- Temperature / Humidity sensor via I²C  
- VOC or gas sensor via I²C or ADC  
- Battery voltage monitoring via ADC or internal measurement

**Power System**
- Coin-cell powered
- Minimal always-on circuitry
- Sensors and peripherals powered only when required
- No charging circuitry

**Wireless Operation**
- BLE advertising at configurable intervals
- GATT-based data access
- Designed to support Matter over BLE commissioning where applicable

---

### 3.3 Power Strategy

- Device remains in deep sleep >99% of the time
- Wake events:
  - Scheduled sensor sampling
  - BLE connection requests
  - User input (button)
- All nonessential peripherals are fully powered down between events

---

## 4. Rev B Architecture (BLE + Wi-Fi, USB-C, LiPo, NFC)

### 4.1 High-Level Block Diagram
```
      ┌───────────────────────────────────────────────┐
      |    Mobile Device / Router / Smart Home App    |
      |  (Wi-Fi LAN, Matter over IP, BLE for pairing) |
      └───────────────────────┬───────────────────────┘
                              │ Wi-Fi / IP Networking
                              ▼
┌──────────────────────────────────────────────────────────────┐
│                          Rev B Device                        │
│                                                              │
│  ┌───────────────┐     I²C / GPIO      ┌───────────────────┐ │
│  │ Temperature / │◄───────────────────►│                   │ │
│  │ Humidity      │                     │  BLE SoC / MCU    │ │
│  ├───────────────┤                     │  - System ctrl    │ │
│  │ VOC / Gas     │◄──── I²C / ADC ────►│  - BLE comms      │ │
│  ├───────────────┤                     │  - Wi-Fi ctrl     │ │
│  │ Battery Sense │◄──── ADC / I²C ────►│                   │ │
│  └───────────────┘                     │                   │ │
│                                        └─────────┬─────────┘ │
│                                                  │           │
│                                                  │           │
│                               ┌───────────────┐  │           │
│       SPI / SDIO / UART ────► │ Wi-Fi Module  │  │           │
│                               │ - IP stack    │  │           │
│                               └───────┬───────┘  │           │
│                                       │          │           │
│  ┌─────────────────────┐              │          │           │
│  │ NFC Interface       │◄─────────────┘          │           │
│  │ - OOB pairing       │                         │           │
│  └───────────┬─────────┘                         │           │
│              │                                   │           │
│        ┌─────▼─────┐                             │           │
│        │ NFC       │                             │           │
│        │ Antenna   │                             │           │
│        └───────────┘                             │           │
│                                                  |           │
│  ┌─────────────────────┐      ┌──────────────────────────┐   │
│  │ USB-C Connector     │      │ LiPo Battery             │   │
│  │ - 5V input          │      │ - Rechargeable cell      │   │
│  └───────────┬─────────┘      └────────────┬─────────────┘   │
│              │                             │                 │
│       ┌──────▼───────────┐                 │                 │
│       │ Charger / Power  │◄────────────────┘                 │
│       │ Path Management  │                                   │
│       │ - Charge ctrl    │                                   │
│       │ - Power mux      │                                   │
│       └──────┬───────────┘                                   │
│              │                                               │
│       ┌──────▼──────────┐                                    │
│       │ Regulators /    │                                    │
│       │ Load Switches   │                                    │
│       └──────_──────────┘                                    │
│                                                              │
│       ┌─────────────────────────────────────────────────┐    │
│       │ Programming / Debug / Factory Test              │    │
│       │ - SWD pads                                      │    │
│       │ - Bed-of-nails test access                      │    │
│       └─────────────────────────────────────────────────┘    │
└──────────────────────────────────────────────────────────────┘
```
---

### 4.2 Functional Description

**BLE SoC / MCU**
- Remains the system master controller
- Manages sensor polling, power domains, and state transitions
- Handles BLE pairing, commissioning, and fallback communication
- Controls Wi-Fi module power and activity

**Wi-Fi Subsystem**
- Provides IP connectivity for Matter over Wi-Fi
- Enabled continuously when USB-powered
- Duty-cycled aggressively when battery-powered

**NFC Subsystem**
- Enables tap-to-pair and out-of-band credential exchange
- Used primarily during commissioning
- Passive when not actively scanned

**Power System**
- USB-C input for continuous operation
- Single-cell LiPo battery with charging and protection
- Power-path management allows seamless switching between USB and battery
- Multiple power domains to isolate high-draw subsystems

---

### 4.3 Power Strategy

- **USB-Powered Mode**
  - Wi-Fi always available
  - Device always reachable
  - No aggressive duty cycling required

- **Battery-Powered Mode**
  - BLE remains low-power always-on interface
  - Wi-Fi enabled only for scheduled sync or explicit requests
  - Sensors and radios powered via load switches

---

## 5. Programming, Debug, and Manufacturing Support

Both revisions include:
- SWD programming interface via pads (no permanent connector)
- Test points for:
  - Power rails
  - Reset
  - Key communication lines
- Designed to support:
  - Firmware flashing
  - Basic RF verification
  - Sensor sanity checks
  - Automated bed-of-nails testing

---

## 6. Architecture Evolution Notes

- Rev B is a strict superset of Rev A
- Firmware architecture is designed to scale without rewrite
- Sensor interfaces and power domains are reusable across revisions
- Future revisions may add additional sensors without altering the core architecture
