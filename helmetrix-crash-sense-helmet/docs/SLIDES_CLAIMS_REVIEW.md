# Review of Claims in the Presentation

Purpose: point out wording that is unsupported, contradictory or too strong, and give safer replacements for the GitHub/README, résumé and any future presentation. Slide numbers refer to the **original** 19-slide deck (they shift by one after slide 3 is removed).

## A. Claims that should change

| Slide | Original wording | Problem | Safer wording |
|---|---|---|---|
| 2, 17 | "Smart **Predictive** Safety Helmet", "**Predictive** accident detection" | The described logic is threshold-based reaction to motion, which happens *after* abnormal motion starts. Nothing predicts a crash. No ML model is described. | "Threshold-based crash detection" / "Smart crash-sensing helmet" |
| 4, 12, 17 | "alcohol detection to **prevent** unsafe riding", "**Prevents drunk driving**", "Preventive feature" | The MQ-3 only gives an analog reading related to alcohol vapour. It is not linked to the ignition, is not a breathalyser, and cannot measure impairment. | "Experimental alcohol-vapour sensor that may trigger a warning; not a measure of impairment and does not stop anyone riding" |
| 3 | *(Problem-statement slide — the team has decided to remove it.)* | It contained uncited statistics (fatality count, "7–10% per minute", "70% of highway accidents") and a sweeping claim about existing smart helmets. | Keep it out unless every figure is cited (source, year, link) |
| 4, 12, 18 | "Reduce response time", "Faster emergency response", "Reduces emergency response time", "Potential to save lives" | Unmeasured. The prototype has not been built or tested; response time also depends on who receives the SMS. | "Aims to shorten the time before someone is notified" |
| 12 | "Automatic accident detection", "Real-time location sharing" | Presented as current features; not built/tested. "Real-time" is not demonstrated (GPS fix and SMS delay unknown). | "Intended to…" ; avoid "real-time" until delays are measured |
| 16 | "Reduces fatalities through faster emergency response" | Outcome claim with no evidence. | "Intended to support SDG 3 by exploring faster emergency notification" |
| 17 | "Novelty" slide | Combining standard sensors/modules in a helmet is a common student/maker idea; novelty cannot be asserted from this deck. | Replace with "What is different in our approach" only after a literature/product search; do not claim novelty |
| 10 | Slide image titled "**Complete Prototype**" | It is an illustration, not a photo of a built device. Methodology slide says assembly is a next step. | Re-label "Conceptual component layout (not a built prototype)" |
| 15 | Render labelled "Arduino/**ESP32** brain", "Sensor Fusion Data: Impact 4.2G, Angle 75°", "Time to help: 4 mins", "© 2026 Helmetrix Safety Labs" | Conflicts with Arduino Nano; the numbers and "4 mins" are invented mock-ups; "Helmetrix Safety Labs" appears to be a fictional organisation. | Do not reuse as evidence; label as concept art or replace with real photos |

## B. Contradictions & unclear points

1. **SOS button:** listed as a prototype component (slide 10) but described as a *future* feature (slide 13). Is it wired/coded or not?
2. **Controller:** Arduino Nano (slides 10, 11, 5 text) vs "Arduino/ESP32" (slide 15 image).
3. **Status:** slides 10–11 read like a finished prototype; slide 14 says components were *ordered* and assembling/testing is the *next step*.
4. **LEDs:** slide 10 lists one "LED indicator"; the illustration shows an LED on D13 plus three LEDs (red/yellow/green) on D9–D11. What do the colours mean?
5. **Wiring illustration issues** (verify each against datasheets before copying anything): the "voltage regulator/charger (TP4056)" is drawn with a "5V output" — a TP4056 is a Li-ion *charger*, not a 5 V regulator; GPS and GSM are both drawn on "TX/RX" of one Nano (which has a single hardware serial port); a pin is labelled "D1" next to the power switch (D0/D1 are the Nano's hardware serial pins); I²C pin names for the MPU6050 are not tied to specific Nano pins.
6. **Slide 13 speaker text** ("If a girl is riding alone at night…") is a presentation script. As a README feature it would need a design for privacy, consent and false-trigger handling; keep it as a clearly labelled idea.
7. **"SIP project":** please confirm what SIP stands for / which programme, for the Acknowledgements.
8. **No numeric thresholds, SMS recipient logic, or alcohol limit** are defined anywhere.

## D. Wording guide for Phase 2 / event descriptions

| Wording used | Issue | Safer wording |
|---|---|---|
| "Processes all sensor data in milliseconds" | Not measured | Omit, or state a measured loop time |
| "Intelligently decides whether it is a real crash or a false alert" | Suggests intelligence/AI; logic undocumented and untested | "Uses threshold-based rules to flag a possible crash" |
| "Crash intelligence" | Same | "Impact/tilt detection" |
| "Instant SMS alerts to police/family" | Not tested; SMS delay unknown; police are not an established recipient | "Sends an SMS to chosen contacts (planned)" |
| "Exact live location… eliminating response delays" | GPS accuracy varies; no-fix cases; delays not eliminated | "Includes a GPS map link when a fix is available" |
| "Real-time medical insight" (MAX30102) | Hobby sensor, not medical; motion-sensitive | "Reads a heart-rate/SpO₂-type signal (experimental)" |
| "Digital witness for accident analysis" (SD log) | Evidential value is unproven | "Logs sensor data for later analysis (planned)" |
| "Actively cools the head to prevent heat fatigue and improve safety" | No data; Peltier power/heat issues | "Experimental Peltier cooling module under evaluation" |
| "Very useful for women's safety" | Not demonstrated | "Could support personal-safety use cases in future work" |
| "Phase 2 prototype… it has [list]" | Reads as if every module works | "Phase 2 design includes…; build and test status: …" |

## C. What the slides *do* support

- A clear problem framing (delayed help after a crash) and a sensible block-level approach.
- A coherent component list for a *basic* prototype.
- A reasonable future-scope list (logging, SOS) — clearly labelled as future work it is fine.
