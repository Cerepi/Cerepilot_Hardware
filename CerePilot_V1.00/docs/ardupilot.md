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
| SERIAL2 | UART8 | MAVLink2, 57600 | On-board Jetson UART1 (also on User Interface_2, pins 11/12) |
| SERIAL3 | UART4 | GPS | On-board u-blox MAX-M10N |
| SERIAL4 | USART1 | MAVLink2 | User Interface_1, RS-232 levels |
| SERIAL5 | USART2 | RCIN | User Interface_1, SBUS |

No port has RTS/CTS flow control. SERIAL4 has no DMA; all other ports do.

## Companion Computer

The Jetson is wired to **SERIAL2**, which defaults to MAVLink2 at 57600 baud.
No jumpers or external wiring are needed. Raise `SERIAL2_BAUD` on both ends if
the link needs more throughput.

The same signal pair is also brought out on User Interface_2, pins 11 and 12.
Leave those pins unconnected when using the on-board Jetson.

## RC Input

RC input is on **SERIAL5** (`SERIAL5_PROTOCOL = 23`, RCIN). Unidirectional
receiver protocols (SBUS, iBUS, DSM and similar) connect to UART_SBUS_RX,
User Interface_1 pin 3, which is USART2 RX. The signal path has no external
inverter, so ArduPilot's internal UART inversion handles SBUS. PPM is not
supported.

For CRSF/ELRS, which is full duplex, connect the receiver's TX to
UART_SBUS_RX (pin 3) and its RX to UART_SBUS_TX (pin 4), and leave
`SERIAL5_OPTIONS` at 0.

Half-duplex protocols that share one wire, such as SRXL2 and FPort, have not
been tested on this board.

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

All outputs support PWM, OneShot and DShot. The outputs pass through TXS0108E
buffers before reaching the connector. Those are bidirectional, so they do not
block the return path on their own, but bidirectional DShot has not been
tested on this board.

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

CAN1 is on User Interface_2 and CAN2 is on User Interface_1. Both ports are
enabled by default and share one driver (`CAN_P1_DRIVER = 1`,
`CAN_P2_DRIVER = 1`), so `CAN_D1_PROTOCOL` sets the protocol for both and
`CAN_D2_PROTOCOL` has no effect. Set `CAN_P2_DRIVER = 2` if CAN2 needs a
protocol of its own.

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
