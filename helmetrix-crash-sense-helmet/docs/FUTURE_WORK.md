# Future Work

> **Everything on this page is a plan, not an existing feature.** The Phase 2 design (ESP32, MAX30102, micro-SD logging, Peltier cooling) is also still unverified; it is treated here as in-design work. Ordered by priority: get a safe, working, documented prototype before adding features.

## Essential (do first)

1. **Assemble and test the basic sensor-to-alert flow** — MPU6050 → Nano → buzzer/LED; then add GPS and SMS. *(Test plan Phase 1)*
2. **Tune crash-detection thresholds** from recorded data; measure false alarms and missed triggers using safe, controlled bench tests. *(Phase 2)*
3. **Add an alert countdown and a cancel button** to reduce accidental emergency messages.
4. **Test GPS, SMS, battery and failure cases** (no fix, no network, low battery, brownout). *(Phase 3)*
5. **Check that SIM800L's 2G service works in the intended test area**; consider a newer cellular module if it does not.
6. **Fix and document the real wiring and power design** (the slide illustration is not a verified design).
7. **Clarify the SOS button**: implement and test it, or mark it as future work consistently.

## Useful next

8. **Validate the MQ-3** carefully as a vapour-response sensor. Do not claim it measures impairment or prevents drunk riding.
9. **Secure the electronics** (enclosure and mounting) **without cutting, drilling or compromising a certified helmet.**
10. **Measure battery life** and add a low-battery indication.
11. **Reproducibility package:** photos, pin table, BOM, code with library versions, demo video.

## Optional / exploratory

12. **Phase 2 modules (MAX30102, Peltier)** — evaluate one at a time with the Phase 4 tests; no medical or safety-benefit claims without measurements.
12b. **Sensor-data logging**, only with informed consent, local storage and privacy safeguards (the slides mention an SD card recording ~30 s before a crash — treat as an idea).
13. **ML experiment**, only after enough *safe, consented* data exists, and only if compared against the threshold-based baseline using a clear metric. Until then the project is **not** AI/ML.
14. **Silent/hidden SOS** — only after a design that addresses consent, visibility and misuse.
15. **Airbag integration** — mentioned in the slides; a major safety-critical engineering effort, out of scope for this prototype.
