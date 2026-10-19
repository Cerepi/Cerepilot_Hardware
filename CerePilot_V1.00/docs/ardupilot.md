# CerePilot V1.00 – ArduPilot

| | |
|---|---|
| Board target | `CerePilotV100` |
| Board ID | `AP_HW_CEREPILOT_V100` (5750) |
| Bootloader | `CerePilotV100_bl.hex` / `CerePilotV100_bl.bin` |

## UART Mapping

| Serial | Port | Default protocol | Connection |
|---|---|---|---|
| SERIAL0 | USB | MAVLink2 | FMU USB, User Interface_1 |
| SERIAL1 | UART7 | MAVLink2 (TELEM1) | User Interface_1 |
| SERIAL2 | UART8 | MAVLink2 | On-board Jetson UART1 (also on User Interface_2, pins 11/12) |
| SERIAL3 | UART4 | GPS | On-board u-blox MAX-M10N |
| SERIAL4 | USART1 | MAVLink2 | User Interface_1, RS-232 levels |
| SERIAL5 | USART2 | RCIN | User Interface_1, SBUS |

No port has RTS/CTS flow control. SERIAL4 has no DMA; all other ports do.

## Companion Computer

The Jetson is wired to **SERIAL2**, which defaults to MAVLink2. No jumpers or
external wiring are needed.

The same signal pair is also brought out on User Interface_2, pins 11 and 12.
Leave those pins unconnected when using the on-board Jetson.

## RC Input

RC input is on **SERIAL5** (`SERIAL5_PROTOCOL = 23`, RCIN). A standard SBUS
receiver can be wired directly. The signal path has no external inverter, so
ArduPilot's internal UART inversion handles SBUS.

## PWM Outputs

Nine PWM outputs, SERVO1–SERVO9. Outputs that share a timer must use the
same rate and protocol:

| Outputs | Timer |
|---|---|
| 1, 2 | TIM3 |
| 3, 4 | TIM1 |
| 5 | TIM2 |
| 6, 7 | TIM4 |
| 8, 9 | TIM15 |

All outputs support PWM, OneShot and DShot. Bidirectional DShot is **not**
supported, because the outputs pass through level translators.

Any unused output can be a GPIO (`SERVOn_FUNCTION = -1`):

| Output | GPIO | Output | GPIO |
|---|---|---|---|
| SERVO1 | 50 | SERVO6 | 55 |
| SERVO2 | 51 | SERVO7 | 56 |
| SERVO3 | 52 | SERVO8 | 57 |
| SERVO4 | 53 | SERVO9 | 58 |
| SERVO5 | 54 | | |

## Sensors

| Sensor | Bus | Default |
|---|---|---|
| BMI088 (IMU 1) | SPI3 | Enabled |
| ISM330DLC (IMU 2) | SPI4 | Enabled |
| BMP585 baro | I²C1 (internal), 0x46 | Enabled |
| BMM350 compass | I²C1 (internal), 0x14 | Enabled |
| SM9541 airspeed | I²C2 (internal), 0x28 | `ARSPD_TYPE = 21`, `ARSPD_BUS = 1` |

External compasses go on the external I²C port (I²C4, bus 2).

## Battery Monitoring

There is no on-board analog battery sensing. The default is an INA2xx I²C
power module on the external I²C port:

- `BATT_MONITOR = 21`

Reboot after changing `BATT_MONITOR`.

## Board Voltage Monitoring

The supply rails are measured through 10 kΩ / 10 kΩ dividers:

| ArduPilot pin | Rail |
|---|---|
| 5 | 5 V input |
| 4 | 3.3 V main |
| 9 | 3.3 V sensor |
| 10 | 3.3 V BMI088 |
| 11 | 1.8 V sensor |

## CAN

CAN1 is on User Interface_2 and CAN2 is on User Interface_1. Both drivers
are enabled by default (`CAN_P1_DRIVER = 1`, `CAN_P2_DRIVER = 1`). Set the
protocol with `CAN_D1_PROTOCOL` and `CAN_D2_PROTOCOL`.

## Storage and Logging

Parameters are stored in FRAM. Logs are written to the microSD card. Insert
the card before power-up.

## LEDs

The green and blue status LEDs use the standard ArduPilot notify patterns.
The green LED also shows bootloader activity.

## Loading Firmware

The board ships with the ArduPilot bootloader. Load `*.apj` firmware from a
ground station, or build and upload it:

```bash
./waf configure --board CerePilotV100
./waf copter --upload
```

To install or recover the bootloader, flash `CerePilotV100_bl.hex` over SWD
with STM32CubeProgrammer, using connect-under-reset
(`-c port=SWD mode=UR`). You can also use DFU.
