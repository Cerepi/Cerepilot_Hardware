# Cerepi Hardware

Public hardware documentation for flight controllers by
[Cerepi](https://www.cerepi.com). Each board has its own folder with
specifications, connector pinouts, mechanical drawings and selected
schematic pages.

| Board | Description | Folder |
|---|---|---|
| **CerePilot V1.00** (current) | STM32H753 FMU + NVIDIA Jetson Orin Nano carrier | [`CerePilot_V1.00/`](CerePilot_V1.00/) |

---

# CerePilot V1.00

**Flight controller and NVIDIA Jetson Orin Nano carrier on a single board.**

CerePilot V1.00 puts an STM32H753 flight management unit (FMU) and a Jetson
Orin Nano companion computer on one 80 × 65 mm PCB. Both share one power
input and one assembly. The FMU runs ArduPilot, and the Jetson talks to it
over a hard-wired MAVLink serial link.

<p align="center">
  <img src="CerePilot_V1.00/docs/cerepilot-v1.00-top-3d.png" alt="CerePilot V1.00 top view" width="480">
</p>

> **Note:** This is not a full design release. Only selected schematic pages
> are published here. Some parts of the design are left out on purpose.

## Photos

| Top | Bottom, with Jetson module and cooler |
|:---:|:---:|
| <img src="CerePilot_V1.00/docs/cerepilot-v1.00-photo-top.jpg" alt="CerePilot V1.00 top side" width="360"> | <img src="CerePilot_V1.00/docs/cerepilot-v1.00-photo-bottom-jetson.jpg" alt="CerePilot V1.00 bottom side with Jetson module installed" width="360"> |

---

## Key Features

**Flight management unit**
- STM32H753VIT6, 480 MHz Cortex-M7, 2 MB flash
- Dual IMU: BMI088 and ISM330DLC
- BMM350 magnetometer
- BMP585 barometer
- SM9541 differential pressure sensor (airspeed), −100 to +100 cmH₂O
- u-blox MAX-M10N GNSS on board, with an MMCX antenna connector
- 32 KB FM25V02A FRAM for parameter storage
- microSD card slot for logging
- 9 PWM / AUX outputs
- 2 × CAN FD, each with an on-board 120 Ω termination
- TELEM UART, SBUS RC input, RS-232 port and external I²C
- 16 MHz TCXO clock source
- Supply-rail monitoring on 5 rails

**Companion computer**
- Connector for an NVIDIA Jetson Orin Nano module (J1100, bottom side)
- Jetson ↔ FMU UART link (FMU SERIAL2 ↔ Jetson UART1), MAVLink2
- 10/100BASE-TX Ethernet
- USB-C (USB 2.0 High-Speed) host port with switched VBUS
- 22-pin CSI camera connector
- Jetson on/off switch

**Power**
- 6.5 V to 26 V input (2S to 6S LiPo)
- Protection against reverse polarity, over/under voltage, over current,
  short circuit and power surges
- Input soft start

## Specifications

| Parameter | Value |
|---|---|
| PCB size | 80.00 × 65.00 mm |
| Mounting holes | 75.30 × 60.30 mm pattern, Ø 2 mm |
| Weight | 55 g |
| Input voltage | 6.5 V – 26 V (2S – 6S LiPo) |
| FMU MCU | STM32H753VIT6 |
| Companion | NVIDIA Jetson Orin Nano |
| Firmware | ArduPilot, board target `CerePilotV100` |

## Board Layout

<p align="center">
  <img src="CerePilot_V1.00/docs/cerepilot-v1.00-user-pinout.png" alt="CerePilot V1.00 user pinout drawing" width="900">
</p>

| Connector | Function | Part number (board / mating) |
|---|---|---|
| Input Power | 4-pin power input | JST SM04B-ZESS-TB / ZER-04V-S |
| Ethernet | 10/100BASE-TX | JST SM04B-ZESS-TB / ZER-04V-S |
| User Interface_1 | CAN2, SBUS, TELEM, RS-232, FMU USB | JST SM16B-ZESS-TB / ZER-16V-S |
| User Interface_2 | PWM 1–9, Jetson UART, I²C, CAN1 | JST SM16B-ZESS-TB / ZER-16V-S |
| USB-C | Jetson USB 2.0 HS host | — |
| Camera_CSI | CSI camera (22-pin) | 54548-2272 |
| GPS antenna | MMCX | — |
| J1100 | Jetson Orin Nano module (bottom side) | — |

See **[pinout.md](CerePilot_V1.00/docs/pinout.md)** for full pin tables.

## Documentation

| Document | Contents |
|---|---|
| [pinout.md](CerePilot_V1.00/docs/pinout.md) | Pinouts for every user connector |
| [hardware.md](CerePilot_V1.00/docs/hardware.md) | Specifications, power, mechanical data and interfaces |
| [ardupilot.md](CerePilot_V1.00/docs/ardupilot.md) | ArduPilot target, serial mapping, sensors and firmware loading |

## Repository Layout

New boards are added as sibling folders next to `CerePilot_V1.00/`.

```
.
├── README.md
├── LICENSE
└── CerePilot_V1.00/
    ├── docs/
    │   ├── pinout.md
    │   ├── hardware.md
    │   ├── ardupilot.md
    │   ├── cerepilot-v1.00-top-3d.png                3D render, top side
    │   ├── cerepilot-v1.00-photo-top.jpg             photo, top side
    │   ├── cerepilot-v1.00-photo-bottom-jetson.jpg   photo, bottom side with Jetson
    │   └── cerepilot-v1.00-user-pinout.png           pinout drawing (PNG preview)
    ├── drawings/
    │   ├── CerePilot_V1.00_User_Pinout.pdf           user pinout / assembly drawing
    │   └── board-outline.png                         PCB outline with dimensions
    └── schematics/
        ├── CerePilot_V1.00_FMU_schematic.pdf         FMU (STM32H753) schematic page
        └── connectors/
            ├── user-interfaces.png                   User Interface_1 / _2
            ├── usb-c.png                             USB-C (Jetson host port)
            ├── ethernet.png                          Ethernet magnetics and connector
            └── camera-csi.png                        Camera CSI connector
```

## Firmware

The board is supported in ArduPilot as `CerePilotV100` (board ID 5750).

```bash
./waf configure --board CerePilotV100
./waf copter --upload
```

See [ardupilot.md](CerePilot_V1.00/docs/ardupilot.md) for the serial port mapping,
default parameters and bootloader recovery.

## Purchase

CerePilot is available from Cerepi: <https://www.cerepi.com>

## License

This documentation is licensed under the
[Creative Commons Attribution-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-sa/4.0/)
(CC BY-SA 4.0). See [LICENSE](LICENSE).

© 2026 Cerepi
