# Changelog — CanPrism DBC ARXML Viewer

## [1.0.0] - 2026-11-13

### Added
- **Free Tier:**
  - Dedicated `.dbc`, `.arxml`, and `.candump`/`.canlog` language support, fast large-file streaming lexers, inline after-line-end bit-span pills, and non-focus-stealing hover cards (`Ctrl+Q` supported).
  - Interactive 2D `8x8` (`64`-Bit Classic CAN) & `64`-Byte (`CAN FD`) **Frame Bit-Packing Matrix** visualizing Motorola Big-Endian (`@0` sawtooth), Intel Little-Endian (`@1`), multiplexor branches (`M` / `m0..mN`), `MSB`/`LSB` markers, and red `!OVR` bit-collision cells with two-way editor caret sync.
  - 29-bit `SAE J1939` arbitration ID breakdown (`Priority`, `PDU1`/`PDU2`, `PGN`, `Source Address`).
- **`[Pro]` Tier (14-Day Free Trial via JetBrains Marketplace):**
  - Non-blocking **Bit-Overlap, `DLC` Byte Overflow & Multiplexor Collision Validator**.
  - **Semantic `.dbc` & `.arxml` Network Diff** (`Alt+Shift+B` and native `Ctrl+D`) stripping `BA_`/`CM_` attribute order and XML `UUID` churn.
  - XXE-hardened pure JDK 21 StAX **Streaming `AUTOSAR 4.x .arxml` Flattener** & 1-click **`.arxml -> .dbc` Converter**.
  - Offline **SocketCAN `candump` & Vector `.asc` Raw Hex Payload Decoder** with live byte-to-signal hover cards (safely ignoring PGP `.asc` armored files).
  - 1-click dependency-free **Clean-Room `C99` Header (`.h`) Generator**, **JSON / CSV Exporter**, and deterministic `HmacSHA256` **NDA Signal & ECU Scrubber** (`Alt+Shift+R`).
