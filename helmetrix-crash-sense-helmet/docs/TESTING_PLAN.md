# Safe Testing Plan

**Ground rules**

- ❌ **Never** crash, drop, strike, throw or otherwise impact a helmet **while anyone is wearing it** or while anyone is nearby in the impact path.
- ❌ Never test with a person who has been drinking, and never ride a vehicle as part of a test.
- ✅ Test the **electronics module** on the bench, on a padded surface, or fixed to a rigid test jig or dummy object. Use gentle, repeatable motions you can safely control.
- ✅ Send test SMS only to **your own team's numbers** with their permission; rate-limit in code so a fault cannot spam.
- ✅ Do not test Li-ion batteries unsupervised; use protected cells and a fire-safe surface.

For each test record: date, who ran it, firmware commit, hardware setup photo, and result in [`tests/test_log_template.csv`](../tests/test_log_template.csv).

## Phase 1 — Bring-up (order matters)

| # | Test | What it demonstrates | Evidence to record |
|---|---|---|---|
| 1 | Nano alone: upload blink sketch | Toolchain and board work | Photo + IDE/board version |
| 2 | MPU6050 raw readout over serial (stationary) | Sensor is wired and responds | Serial screenshot; noise range at rest |
| 3 | MPU6050 orientation check (tilt by hand) | Axes understood and documented | Axis diagram + readings per orientation |
| 4 | GPS NMEA output, outdoors | Module works; time-to-first-fix | Screenshot, TTFF, fix validity |
| 5 | GSM: AT command check, SIM registration, signal strength | Network is available and module powered correctly | AT responses, operator, signal value, supply voltage measured |
| 6 | GSM: send one SMS to a team number | SMS path works | Received-SMS screenshot, delay |
| 7 | Buzzer/LED/SOS button individually | Outputs/inputs work | Short video |
| 8 | MQ-3 readout after warm-up | Sensor responds, baseline value | Baseline over time; warm-up duration used |

## Phase 2 — Detection tuning (bench only)

| # | Test | What it demonstrates | Evidence |
|---|---|---|---|
| 9 | Log raw accel/gyro to a laptop for **normal** motions of the module (carry, set down, tilt, shake gently, tap) | What "normal" looks like | CSV + plot per motion |
| 10 | Log **harsh-but-safe** events with the module in a padded box (e.g. a controlled low drop of the box onto foam from a fixed small height, or a firm tap) | Signature of a sharp impact vs. normal | CSV + plot, drop height, surface |
| 11 | Choose thresholds from the data (document how) | Thresholds are evidence-based, not guessed | Plot with the threshold line; written rationale |
| 12 | **False-alarm test:** repeat many everyday motions (≥ `[n]` trials) with the final thresholds | False-alarm rate for the tested motions | Table: motion type, trials, triggers |
| 13 | **Missed-trigger test:** repeat the safe impact events | Missed-detection rate for the tested events | Table: event, trials, detections |
| 14 | Re-run after any threshold change | No regression | Versioned results |

State clearly that results only cover the *tested bench events* — they say nothing about real crashes.

## Phase 3 — System & failure cases

| # | Test | What it demonstrates | Evidence |
|---|---|---|---|
| 15 | Full chain on bench: trigger → GPS → SMS → buzzer | End-to-end flow works | Video, SMS screenshot, time from trigger to SMS |
| 16 | GPS with no fix (indoors) | Fallback text works; no stale position as current | SMS received, fix status |
| 17 | GSM with no network / SIM removed | Failure handled, no spam | Serial log, SMS count |
| 18 | Low battery / brownout during send | Power design adequate | Voltage trace, resets observed |
| 19 | Battery runtime (idle monitoring) | Honest runtime figure | Start/stop times, capacity, load |
| 20 | Countdown + cancel (when implemented) | Accidental sends can be cancelled | Trials: cancelled vs sent |
| 21 | MQ-3 qualitative response (safe, ventilated, no people drinking) | Sensor reacts to vapour; limits | Baseline vs response readings; written caveats |
| 22 | Vibration/handling test of the mounted module | Mounting stays rigid; no disconnects | Photos, wiggle test notes |

## What to publish

- Raw data (CSV), plots, test log, photos/videos, and the exact firmware commit.
- A short results table with **trial counts**. Do not report percentages from tiny samples as if they were performance guarantees.

## Phase 4 — Phase 2 modules (bench only, one at a time)

Extra ground rules: no Peltier or high-current test on a person or inside a helmet until its surface temperatures and current have been measured on the bench with a heat sink; keep a fire-safe surface and supervision for any Li-ion pack.

| # | Test | What it demonstrates | Evidence |
|---|---|---|---|
| 23 | ESP32 bring-up: blink, serial, board/pin map | Toolchain and board work; UART pins known | Photo, board model, pin table |
| 24 | MAX30102 at rest, on a consenting team member's fingertip (not under any impact) | Sensor responds and what signal it gives | Raw readings, conditions, caveat that it is not a medical reading |
| 25 | MAX30102 with gentle motion | How motion corrupts readings | Plot of signal quality vs motion |
| 26 | Micro-SD: write a log file, then cut power mid-write | Logging works and how it fails | File contents, corruption observed or not |
| 27 | Rolling-buffer log around a bench trigger | The "seconds before the event" idea is feasible | Log excerpt with timestamps |
| 28 | Peltier on bench with heat sink: measure current, hot/cold-side temperature over time | Real power draw and whether it cools at all | Current, voltage, temperatures vs time, battery drain |
| 29 | Total power budget with all modules running | Whether the pack can supply the system and for how long | Current trace, runtime |
