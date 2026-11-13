# Motorola Big-Endian (`@0` Sawtooth) vs. Intel Little-Endian (`@1`) CAN Bit Numbering

One of the costliest sources of silent Hardware-in-the-Loop (HIL) and ECU firmware bugs in automotive `.dbc` and `AUTOSAR .arxml` files is mixing up **Motorola Big-Endian (`@0`)** and **Intel Little-Endian (`@1`)** `startBit` semantics.

## 1. Flat Bit Indexing Inside a CAN / CAN FD Payload

Every byte `B` (`0 .. DLC-1`) contains 8 bits numbered `0` (LSB) through `7` (MSB):

```text
flatBit = byteIndex * 8 + bitInByte
```

- **Byte 0:** `7 (MSB) | 6 | 5 | 4 | 3 | 2 | 1 | 0 (LSB)`
- **Byte 1:** `15 (MSB) | 14 | 13 | 12 | 11 | 10 | 9 | 8 (LSB)`
- **Byte 2:** `23 (MSB) | 22 | 21 | 20 | 19 | 18 | 17 | 16 (LSB)`

## 2. Intel Little-Endian (`@1+` / `MOST-SIGNIFICANT-BYTE-LAST`)

- **`startBit` is the Least Significant Bit (`LSB`)** of the signal.
- Bits advance monotonically upward (`startBit .. startBit + bitLength - 1`).
- **Example:** `SG_ VehicleSpeedKph : 24|16@1+ (0.01,0) [0|300] "km/h"` occupies bits `24..39` (`Byte 3` = low byte, `Byte 4` = high byte).

## 3. Motorola Big-Endian (`@0+` / `MOST-SIGNIFICANT-BYTE-FIRST`)

- **`startBit` is the Most Significant Bit (`MSB`)** of the signal.
- Bits step **downward** within the current byte (`bitInByte - 1`). When crossing `bitInByte == 0` (the LSB of the current byte), the next bit jumps in a **sawtooth wrap** to the MSB (`bit 7`) of the **next byte** (`(byteIndex + 1) * 8 + 7`).
- **Example:** `SG_ RequestedTorque : 15|16@0- (0.1,0) [-500|500] "Nm"`
  - Starts at `bit 15` (`Byte 1, bit 7`) -> `15, 14, 13, 12, 11, 10, 9, 8` (`Byte 1`).
  - Sawtooth jump at byte boundary -> `23, 22, 21, 20, 19, 18, 17, 16` (`Byte 2`).
- **Common Collision Trap:** Placing another signal at `startBit = 16` (`Byte 2`) collides directly with the low byte of `RequestedTorque`, even though `15 + 16` looks unrelated at a glance! **CanPrism DBC ARXML Viewer** highlights these sawtooth overlaps in bright red (`!OVR`) inside the 2D Bit Matrix and flags `SIGNAL_BIT_OVERLAP` offline.
