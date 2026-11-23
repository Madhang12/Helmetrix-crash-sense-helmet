# Project Status & Evidence Matrix

Material reviewed so far: the project presentation (19 slides, Phase 1 design), the team's LinkedIn description of the Phase 2 prototype, three photos (a bench photo of components, a screenshot of The Hindu article, an event photo of the presentation), and the university's event write-ups. **No code, test logs, wiring diagram of the actual build, or demo video have been supplied.** Slides and posts are treated as project material, not proof.

## Inventory

| Item | What it contains |
|---|---|
| Presentation (19 slides) | Phase 1 design: Arduino Nano, MPU6050, Neo-6M, SIM800L, MQ-3, buzzer/LED, SOS button. Methodology slide says parts were ordered and assembly/testing are next. |
| Team's LinkedIn description | Phase 2 prototype (team says "Phase 2 of 4"): ESP32, MPU6050, SIM800L, NEO-6M, MAX30102, micro-SD, Peltier cooling. Event details. Some wording is stronger than the evidence (see claims review). |
| Bench photo | Loose components and jumper wires on a bench: breadboard, battery holder with cell, power module, buzzer, micro-SD module, small sensor boards, LEDs/resistors, a switch, an antenna spring, a Peltier-type plate. Microcontroller, GPS, GSM and heart-rate modules are **not identifiable** in the photo. Shows assembly under way, not a working circuit. |
| The Hindu screenshot | "Woxen University students showcase smart safety helmet at road safety drive", published 16 Apr 2026, Sangareddy, The Hindu Bureau. The visible part covers the programme and the Additional DGP's road-safety message; the rest of the article was not visible. |
| Event photo | Team presenting on stage at Woxsen University, with a display board listing helmet features. |

## Status matrix

Legend: ✔ stated/shown · ✘ no · ? unknown — needs the team's confirmation.

| Item | Described | Built | Tested | Notes |
|---|:-:|:-:|:-:|---|
| Microcontroller | ✔ Nano (Ph.1) / ESP32 (Ph.2) | ? | ✘ | Which is the current build? |
| MPU6050 motion sensing | ✔ | ? | ✘ | A small sensor board is visible in the photo; model unconfirmed |
| Rule/threshold crash detection | ✔ | ? | ✘ | No numeric thresholds or logic documented |
| NEO-6M GPS | ✔ | ? | ✘ | |
| SIM800L SMS with map link | ✔ | ? | ✘ | |
| Buzzer | ✔ | ? | ✘ | Buzzer visible in photo |
| LED, SOS button | ✔ (Ph.1) | ? | ✘ | SOS is also listed as "future scope" on slide 13 |
| MQ-3 alcohol sensor | ✔ (Ph.1) | ? | ✘ | Not mentioned in Phase 2 description |
| MAX30102 heart rate / SpO₂ | ✔ (Ph.2) | ? | ✘ | |
| Micro-SD logging | ✔ (Ph.2) | ? | ✘ | Micro-SD module visible in photo |
| Peltier cooling | ✔ (Ph.2) | ? | ✘ | Peltier-type plate visible in photo |
| Temperature sensor (DHT11-type) | ✘ not described | ? | ✘ | Appears in photo; purpose? |
| Battery / power | ✔ | ? | ✘ | Battery holder and power module visible; capacity/runtime unknown |
| Alert countdown / cancel | ✘ | ✘ | ✘ | Planned only |
| ML / AI | **Not described** | ✘ | ✘ | Do not describe as AI/ML |

## Events (separately verifiable from public sources)

| Fact | Source |
|---|---|
| "Arrive Alive" Road Safety Awareness Programme at Woxsen University, Sangareddy, 16 April 2026 | The Hindu (published 16 Apr 2026, "on Thursday"); university news write-up dated 17 Apr 2026 |
| Over 500 students, police officials and academic leaders attended | University write-up |
| Press coverage of the student smart helmet | The Hindu, 16 Apr 2026 |

## One-line status (use until evidence changes it)

> HELMETRIX is a student prototype in development (Phase 2 of 4 per the team). The design has been presented publicly; assembly has begun; firmware and testing results are not yet documented.

## What would upgrade a row

| To claim… | Add this evidence |
|---|---|
| Built | Photo of the actual wired module + pin table + code in `firmware/` |
| Works | Test-log rows in `tests/` (date, setup, expected vs observed) + serial screenshot or the received SMS |
| Tested | The same, repeated enough to state a trial count |
