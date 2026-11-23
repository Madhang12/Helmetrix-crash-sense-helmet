# Firmware

**No firmware has been added yet.**

When you add code, please include:

- `helmetrix.ino` (or a PlatformIO project) — plus a note on which parts you wrote and which are from libraries/examples (credit sources).
- Board and toolchain: Arduino IDE / PlatformIO version, board = Arduino Nano (`[old/new bootloader?]`).
- Libraries with exact versions (e.g. MPU6050 library, GPS parser, serial libraries) — `[fill in]`.
- A config section for thresholds with a comment on where each value came from (link to test data).
- **Never** hard-code real phone numbers; use a placeholder like `+00XXXXXXXXXX` and document how to set it locally.

Suggested structure:

```
firmware/
├─ helmetrix/helmetrix.ino
├─ config.example.h     # placeholders only
└─ sketches_bringup/    # small test sketches: mpu_read, gps_read, gsm_at, sms_send
```
