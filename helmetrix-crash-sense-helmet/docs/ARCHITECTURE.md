# Architecture & Technical Review (design-stage)

> Everything below is **intended design plus review notes**. Nothing here has been built or measured. Items marked **Verify** must be checked against the actual datasheets/boards you own.

## 1. Data flow (as described in the slides)

```
MPU6050 --(I2C: accel + gyro)--> Arduino Nano
                                   |
                     compare against fixed thresholds
                                   |
                         "possible crash" flag
                                   |
              read latest fix <--- Neo-6M GPS (serial NMEA)
                                   |
                      build SMS text + maps link
                                   |
                       SIM800L (AT commands) --> SMS to saved number
                                   |
                           Buzzer + LED indication

Side inputs:  MQ-3 (analog) -> warning threshold -> buzzer/LED
              SOS button (digital) -> manual alert   [status: to confirm]
Power:        Battery -> charger/regulator -> Nano + modules
```

## 2. Text block diagram

```
+----------------------------- HELMET MODULE -----------------------------+
|  Sensing            Processing              Output / Communication      |
|  --------           ----------              ----------------------      |
|  MPU6050  ------->                          Buzzer, LED                 |
|  MQ-3     ------->  Arduino Nano  ------->  SIM800L --> SMS (2G) -----> | Contact's phone
|  SOS btn  ------->  (thresholds)            (needs SIM + signal)        |
|  Neo-6M   ------->  (reads GPS)                                          |
|                                                                         |
|  Battery -> charger/regulator -> all modules (see power notes)          |
+-------------------------------------------------------------------------+
```

## 3. Crash-detection logic — review

**Described:** tilt angle above a safe limit, sudden high acceleration, rapid fall movement (slide 7). **Not defined:** actual numbers, sample rate, filtering, duration rules, what happens after a trigger.

Concerns and suggestions:

- **Thresholds are unknown** — they must come from recorded data, not guesses. Start by logging raw data and plotting it (see test plan).
- **Sensor range:** MPU6050 accelerometer full-scale range is configurable (±2/4/8/16 g by datasheet) and the default is the smallest. A real impact can exceed a small range and saturate. **Verify** the range you set and what impact levels you expect to see.
- **Orientation/tilt alone is a weak trigger:** riders tilt the head, bend, or take the helmet off. A crash trigger usually needs more than one condition (e.g. a spike *and* a period of no movement afterward). This is a design idea to test, not a proven rule.
- **Helmet ≠ vehicle:** a head-mounted sensor sees head motion (nodding, shoulder checks, helmet drops) rather than bike motion.
- **Likely false alarms:** dropping or tossing the helmet, placing it on a hook roughly, riding over potholes/speed bumps, hand-carrying a swinging helmet.
- **Likely misses:** low-speed falls, slow tip-overs, glancing impacts below the threshold, sensor mounted loosely so the helmet moves around it.
- **Sampling:** if the main loop is slowed by GPS parsing or GSM waiting, impact samples can be missed. Design the loop so sensor reading is never blocked by SMS/GPS handling.
- **Calibration:** gyro/accel offsets drift with temperature; zero them at start-up on a still helmet.
- **Not ML:** this is a rule-based detector. Call it "threshold-based".

## 4. Sensor placement

- Mount the MPU6050 **rigidly**, in a fixed orientation relative to the helmet, away from padding that can move independently. Record the axis orientation in the docs.
- Do not drill, cut or glue to the helmet shell or EPS liner; use a method the helmet manufacturer permits (see safety doc). `[Decide and document your mounting method.]`
- Keep the GPS antenna with a clear view of the sky (top of helmet), and keep the GSM antenna away from the GPS module to limit interference. **Verify** on the bench.

## 5. GPS behaviour — review

- **Cold start** can take minutes; indoors it may never get a fix. The code must handle "no fix yet".
- Decide what the SMS says if there is no fix (e.g. "location unavailable" or last known fix with its age). **Never present a stale position as current.**
- Parse validity flags from the NMEA data rather than trusting zeros.
- Record time-to-first-fix and the fix status at the moment of an alert.

## 6. GSM (SIM800L) behaviour — review

- **2G only.** Whether your operator still provides GSM service in the deployment area must be confirmed (see §9).
- **Power is the most common failure point.** The module is widely documented as needing a supply in roughly the 3.4–4.4 V range and short bursts of high current (≈ up to 2 A) during transmission. **Verify in the datasheet** and measure on a scope/meter. Powering it from a Nano's 5 V pin typically fails or resets.
- **Logic levels:** SIM800L's serial pins are generally 2.8 V logic; a 5 V Nano TX may need level shifting. **Verify** for your board.
- **Serial ports:** the Nano has one hardware UART, which is shared with USB. GPS and GSM both need serial, so a plan (SoftwareSerial, a board with more UARTs, or switching one at a time) is required; SoftwareSerial listens to only one port at a time and can drop data at higher baud rates.
- **Failure cases to handle:** no SIM/PIN lock, no network registration, low signal, SMS send failure, module reset on brownout. Decide on retries and a rate limit so one fault cannot send dozens of messages.

## 7. Power requirements — review

No battery capacity, runtime, or current budget is given in the slides. To do:

1. List the supply voltage and measured current of each module (idle and peak).
2. Note the MQ-3 heater is a continuous load (datasheets typically quote a few hundred mW — **verify**), which strongly affects battery life.
3. Use a proper Li-ion charging/protection circuit and a regulator suited to the SIM800L's peak current. Lithium cells near a head need protection circuitry and a safe enclosure — **do not skip this**.
4. Record battery life from a real test.

## 8. Likely failure cases (checklist)

| Failure | Consequence | What to handle/record |
|---|---|---|
| False trigger | Unwanted SMS to family | Countdown + cancel (planned); false-alarm count |
| Missed crash | No alert | Missed-trigger count in bench tests |
| No GPS fix | SMS without location | Fallback text; time-to-fix |
| No GSM network | SMS never arrives | Retry policy; local buzzer still works |
| Brownout/reset | Device restarts mid-alert | Power budget test |
| Battery flat | Device silent | Low-battery indication |
| Loose sensor | Wrong readings | Mounting method |
| Water/heat/vibration | Hardware failure | Enclosure notes (future) |

## 9. Deployment-region compatibility (verify before relying on it)

- **2G/GSM availability:** many countries have switched off or reduced 2G. Confirm with your operator that GSM voice/SMS service exists in your test area and that your SIM works on it. The SIM800L is quad-band GSM only — no 3G/4G/LTE.
- **Band support:** confirm the module's bands cover your operator's GSM bands.
- **SIM/SMS rules:** check your operator's rules for automated SMS from a module and for SMS to numbers on other networks.
- **Antennas & radio rules:** use a proper antenna; check local rules on radio equipment before any wider use.
- **GPS:** generally works globally, but check module variants and antenna.
- **Helmet standards:** in India helmets are subject to a BIS standard (commonly cited as IS 4151). Check what applies in your region before attaching anything to a helmet.

## 10. MQ-3 alcohol sensor — what it can and cannot tell you

**What it is:** a heated metal-oxide sensor whose resistance changes in the presence of alcohol (ethanol) vapour. The board outputs an analog level (and sometimes a digital pin with an adjustable comparator).

**Limitations:**

- It responds to **ethanol vapour in the air near it**, not blood-alcohol concentration (BAC). Breath-to-blood conversions require calibrated, certified instruments and procedures.
- **Cross-sensitivity:** other vapours (e.g. some solvents, sanitiser, perfume, smoke, fuel) can also change the reading.
- **Environment:** temperature, humidity and airflow change the reading; inside a helmet these vary widely.
- **Mouth alcohol / recent drinks / mouthwash** can cause high short-term readings regardless of impairment.
- **Warm-up & drift:** needs a pre-heat period, and datasheets/vendors commonly recommend a longer initial burn-in (**verify**); readings drift with ageing.
- **Calibration:** the cheap breakout boards are uncalibrated. Getting meaningful numbers needs known ethanol concentrations and a reference instrument — not available to a typical student lab.
- **Placement:** a sensor in a helmet may not sample breath reliably (visor, vents, wind).

**What you can say:** "The prototype reads an analog value from an MQ-3 sensor, which rises when alcohol vapour is present near it." Optionally, after tests: "In our trials with `[describe safe test method]`, the reading rose above baseline `[n]` out of `[n]` times."

**What you cannot say:** that it measures BAC, detects drunk riding, determines impairment, is legally valid, or prevents drunk driving.

**Safe testing note:** do not test with people drinking. Use a small, safe cotton-swab/vapour exposure of a household item in a ventilated area, away from flames, if at all; record it as a *qualitative response test* only.

## 11. Privacy, consent, reliability, safety

- **Location is sensitive personal data.** The device sends it to a third party. The wearer must understand and agree; recipients should agree to receive alerts.
- **Misuse risk:** the same hardware could be used to track someone without consent. Document that the intended use is wearer-controlled, and avoid features that send location covertly — rethink the slide-13 "silently send location" idea (design it with consent, visible indication, and an easy off switch).
- **Data minimisation:** do not log or store location/sensor data unless necessary; if you do (future work), get informed consent, store locally, and document retention/deletion.
- **Reliability:** an emergency device that fails silently can give false confidence. State clearly that it is a prototype, and that it must not replace calling emergency services.
- **False alarms** cost trust and can waste responders' time: add confirmation (countdown + cancel) before any real emergency use.
- **Physical safety:** batteries, wiring and rigid parts next to the head; do not compromise the helmet's protective structure.
- **Legal:** India's Digital Personal Data Protection Act, 2023 and similar laws elsewhere may apply if you ever collect other people's data. This is not legal advice — consult a qualified person.

## 12. Phase 2 additions — design review notes

> The team describes a Phase 2 prototype with an ESP32, MAX30102, micro-SD and a Peltier cooler. None of this is verified. Which parts of Phase 1 (MQ-3, SOS button, LED) carry over is `[to confirm]`.

**ESP32 instead of Arduino Nano**
- Generally easier for this design: 3.3 V logic (closer to the SIM800L's serial levels — still **verify**), and more than one hardware UART, which removes the Phase 1 problem of sharing one serial port between GPS and GSM. Confirm which pins your board maps to the extra UARTs.
- The ESP32 itself does not supply the SIM800L's peak current; the GSM module still needs its own suitable supply (see §6–7).
- Wi-Fi/Bluetooth radios are not needed for this design; leave them off to save power unless used.

**"Decides whether it is a real crash or a false alert"**
- Describe the real logic in the README once written (e.g. which thresholds, what duration, any post-impact stillness check). Unless a model is trained and evaluated, this is rule-based logic, not AI/ML.

**MAX30102 (heart rate / SpO₂)**
- It is an optical sensor that needs steady skin contact and is sensitive to motion, ambient light, pressure and skin tone. It is a hobby-grade part, **not a medical device**.
- Inside a helmet, a stable contact point is hard; readings during or just after an impact will likely be unreliable. Claim at most "reads a pulse-like signal under bench conditions" until measured.
- Heart-rate/SpO₂ values are **health data**: collect only with informed consent and decide in advance who may receive them.

**Micro-SD logging**
- Needs a design for what is stored (e.g. a rolling buffer in RAM that is written out on trigger — the slides mention the last ~30 s), file flushing so data survives power loss, and log size.
- Logs of location and speed are sensitive; consent, local storage, and a delete path are required (see safety doc).

**Peltier cooling**
- Thermoelectric modules commonly draw several amps; **check the exact part's datasheet** and compare with what a helmet-sized battery can deliver and for how long.
- The hot side needs effective heat removal; without it the module heats the head side too. Condensation, weight, and electrical safety next to the head are real concerns, and mounting must not compromise the helmet.
- Treat it as a separate experiment with its own tests. Don't claim it "prevents heat fatigue" or "improves safety" without measurements.

**Temperature sensor (DHT11-type, visible in the bench photo)**
- Purpose not documented (`[cooling control? ambient logging?]`). Note that DHT11-class sensors are slow and low-accuracy.

**Power budget (much larger than Phase 1)**
- Sum the measured peak and average current of the ESP32, SIM800L bursts, GPS, MAX30102, SD card, and Peltier. The Peltier alone may dominate. A battery/charger design with protection is needed before any wearable use.
