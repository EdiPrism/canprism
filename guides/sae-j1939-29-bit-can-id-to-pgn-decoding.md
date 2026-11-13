# Decoding 29-Bit `SAE J1939` Arbitration IDs into Priority, `PGN`, and Source Address

Heavy commercial vehicles, off-highway equipment, marine propulsion, and high-voltage EV traction inverters frequently encode **SAE J1939** Parameter Group Numbers (`PGN`s) inside 29-bit extended CAN arbitration IDs (`0x80000000` flag set in `.dbc` `BO_` headers).

## 29-Bit Identifier Bit Layout

| Bits | Width | Field | Formula |
| :--- | :---: | :--- | :--- |
| `28..26` | 3 bits | **Priority (`P`)** | `(id29 >> 26) & 0x7` |
| `25` | 1 bit | **Extended Data Page (`EDP`)** | `(id29 >> 25) & 0x1` |
| `24` | 1 bit | **Data Page (`DP`)** | `(id29 >> 24) & 0x1` |
| `23..16` | 8 bits | **PDU Format (`PF`)** | `(id29 >> 16) & 0xFF` |
| `15..8` | 8 bits | **PDU Specific (`PS`)** | `(id29 >> 8) & 0xFF` (Destination Address if `PF < 240`, Group Extension if `PF >= 240`) |
| `7..0` | 8 bits | **Source Address (`SA`)** | `id29 & 0xFF` |

## `PDU1` vs. `PDU2` `PGN` Calculation

- **`PDU1` Peer-to-Peer (`PF < 240` / `0xF0`):** `PS` holds the target ECU Destination Address and is **zeroed** in the `PGN`:
  `PGN = (EDP << 17) | (DP << 16) | (PF << 8)`
- **`PDU2` Broadcast (`PF >= 240` / `0xF0`):** `PS` is a Group Extension included in the `PGN`:
  `PGN = (EDP << 17) | (DP << 16) | (PF << 8) | PS`

**Worked Example (`EEC1` — `BO_ 2364539904`):**
- Strip `.dbc` extended flag (`& 0x1FFFFFFF`) -> `0x0CF00400`
- `Priority = 3`, `PF = 0xF0` (`240 >= 240` -> `PDU2`), `PS = 0x04`, `SA = 0x00`
- `PGN = 0xF004 = 61444` (`Electronic Engine Controller 1`).
