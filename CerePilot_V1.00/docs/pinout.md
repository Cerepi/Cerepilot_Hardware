# CerePilot V1.00 – Connector Pinout

The tables below follow the user pinout drawing,
[`drawings/CerePilot_V1.00_User_Pinout.pdf`](../drawings/CerePilot_V1.00_User_Pinout.pdf),
with one correction on the Ethernet connector (see below).
The 3D view in the drawing is the top side of the board. Pin 1 of each
connector is marked on the drawing.

All signals are 3.3 V logic unless the table says otherwise.

![User pinout drawing](cerepilot-v1.00-user-pinout.png)

## Input Power

JST SM04B-ZESS-TB (mating: JST ZER-04V-S)

| Pin | Signal |
|---|---|
| 1 | Input power positive |
| 2 | Input power positive |
| 3 | Input power ground |
| 4 | Input power ground |

Input range: 6.5 V to 26 V (2S to 6S LiPo).

## 10/100BASE-TX Ethernet

JST SM04B-ZESS-TB (mating: JST ZER-04V-S)

| Pin | Signal |
|---|---|
| 1 | MTR0_P |
| 2 | MTR0_N |
| 3 | MTR1_P |
| 4 | MTR1_N |

> **Errata:** the user pinout drawing labels pin 4 as `MTR0_N`. That label is
> wrong. Pin 4 is `MTR1_N`, as shown in the Ethernet schematic.

The connector carries the transformer-side pairs (MTR0 and MTR1) from the
WE-LAN 7490100111A magnetics. The connector shield is tied to ground. See
[`schematics/connectors/ethernet.png`](../schematics/connectors/ethernet.png).

## User Interface_1

JST SM16B-ZESS-TB (mating: JST ZER-16V-S)

| Pin | Signal | Notes |
|---|---|---|
| 1 | FDCAN2_L | CAN2, 120 Ω termination fitted on board |
| 2 | FDCAN2_H | CAN2 |
| 3 | UART_SBUS_RX | RC input |
| 4 | UART_SBUS_TX | |
| 5 | USART_RX_TEL | TELEM1 RX |
| 6 | USART_TX_TEL | TELEM1 TX |
| 7 | USART/RS232_RX_EXT | RS-232 signal levels |
| 8 | USART/RS232_TX_EXT | RS-232 signal levels |
| 9 | USB_DC_N | FMU USB D− |
| 10 | USB_DC_P | FMU USB D+ |
| 11 | GND/Reserve | |
| 12 | GND/Reserve | |
| 13 | GND/Reserve | |
| 14 | GND/Reserve | |
| 15 | GND/Reserve | |
| 16 | GND/Reserve | |

## User Interface_2

JST SM16B-ZESS-TB (mating: JST ZER-16V-S)

| Pin | Signal | Notes |
|---|---|---|
| 1 | GND | |
| 2 | PWM/AUX_1 | |
| 3 | PWM/AUX_2 | |
| 4 | PWM/AUX_3 | |
| 5 | PWM/AUX_4 | |
| 6 | PWM/AUX_5 | |
| 7 | PWM/AUX_6 | |
| 8 | PWM/AUX_7 | |
| 9 | PWM/AUX_8 | |
| 10 | PWM/AUX_9 | |
| 11 | USART_TX_JTS | FMU ↔ Jetson UART, see note below |
| 12 | USART_RX_JTS | FMU ↔ Jetson UART, see note below |
| 13 | I2C_SDA | External I²C |
| 14 | I2C_SCL | External I²C |
| 15 | FDCAN1_L | CAN1, 120 Ω termination fitted on board |
| 16 | FDCAN1_H | CAN1 |

> **Shared Jetson UART:** pins 11 and 12 are the same signal pair that links
> the FMU (SERIAL2) to the Jetson's UART1 on the board. Leave these pins
> unconnected when the on-board Jetson is in use. An external device would put
> a second transmitter on the same line.

## Camera_CSI

22-pin connector, 54548-2272. See
[`schematics/connectors/camera-csi.png`](../schematics/connectors/camera-csi.png).

| Pin | Signal | Pin | Signal |
|---|---|---|---|
| 1 | Jetson_3V3 | 12 | CSI0_D0_N |
| 2 | CAM_I2C_SDA | 13 | GND |
| 3 | CAM_I2C_SCL | 14 | CSI1_CLK_P |
| 4 | GND | 15 | CSI1_CLK_N |
| 5 | CAM0_MCLK | 16 | GND |
| 6 | CAM0_PWDN_C | 17 | CSI1_D1_P |
| 7 | GND | 18 | CSI1_D1_N |
| 8 | CSI0_D1_P | 19 | GND |
| 9 | CSI0_D1_N | 20 | CSI1_D0_P |
| 10 | GND | 21 | CSI1_D0_N |
| 11 | CSI0_D0_P | 22 | GND |

The connector's mounting pins (MP1, MP2) are tied to ground. CAM0_PWDN is
buffered by an SN74LV1T125 on the Jetson 3.3 V rail.

## USB-C

USB 2.0 High-Speed host port of the Jetson. The data lines pass through a
WE-CNSA common-mode choke and an ESDAVLC6-2BLY ESD protection array. VBUS is
switched by a TPS2069 power switch. See
[`schematics/connectors/usb-c.png`](../schematics/connectors/usb-c.png).

The FMU's own USB is on **User Interface_1**, pins 9 and 10.

## Other Connectors

| Connector | Function |
|---|---|
| GPS MMCX | Antenna for the on-board u-blox MAX-M10N |
| J1100 | Jetson Orin Nano module connector, bottom side |
| Jetson Off/ON | Slide switch for the Jetson |
