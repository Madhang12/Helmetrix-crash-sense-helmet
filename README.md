# HELMETRIX - Crash Sense Helmet System

HELMETRIX is a student project on a helmet that can sense a possible crash and send an SMS with the rider's GPS location to a saved contact, without needing a phone.

## Problem

Two-wheeler riders can get badly hurt in a crash, and an injured rider may not be able to call for help. A helmet that has its own GPS and GSM module doesn't depend on a paired phone, which may be damaged or out of reach after a fall. This project looks at how a low-cost version of that could work.

## What it is meant to do

1. Read motion data from an MPU6050 (accelerometer and gyroscope).
2. Use fixed thresholds to decide if the motion looks like a crash. This is rule-based, not machine learning.
3. Read the position from a NEO-6M GPS module.
4. Send an SMS with a Google Maps link through a SIM800L GSM module.
5. Sound a buzzer and light an LED locally.

## Components

The first design, from our project presentation, uses an Arduino Nano, MPU6050, Neo-6M GPS, SIM800L, an MQ-3 alcohol sensor, a buzzer, an LED, an SOS button, and a battery with a power module.

The second design, which we presented in April 2026, moves to an ESP32 and adds a MAX30102 heart-rate/SpO2 sensor, a micro SD module for logging data, and a Peltier module for cooling the head. It doesn't mention the MQ-3, buzzer, LED or SOS button, so whether those stay is still to be decided.

| | First design | Second design |
|---|---|---|
| Microcontroller | Arduino Nano | ESP32 |
| Motion sensor | MPU6050 | MPU6050 |
| GPS | Neo-6M | NEO-6M |
| GSM | SIM800L | SIM800L |
| Alcohol sensor | MQ-3 | not mentioned |
| Buzzer, LED, SOS button | yes | not mentioned |
| Heart rate / SpO2 | no | MAX30102 |
| Data logging | future idea | micro SD |
| Cooling | no | Peltier |

There is a photo of components laid out on the bench during assembly in `media/photos/`. It shows the parts and wiring we were working with, not a finished circuit.

## How it works

```
MPU6050 -> microcontroller -> threshold check -> possible crash?
                                                      |
NEO-6M GPS -> read location -------------------------+
                                                      v
                                 SIM800L sends SMS with map link
                                 buzzer + LED
```

This is the planned flow, not a verified wiring diagram. More detail is in [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md).

Code, wiring and setup

### What is included

- `firmware/include/crash_detector.h` and `firmware/src/crash_detector.cpp`: portable C++ rule-based logic. It receives acceleration in g and angular velocity in degrees per second. It reports a candidate only when both acceleration magnitude and rotation magnitude meet configurable thresholds.
- `tests/test_crash_detector.cpp`: host-side checks using hand-written synthetic samples.

This is **not microcontroller firmware**. It does not read an MPU6050, access GPS, send SMS, sound a buzzer, or control an SOS/alcohol sensor. The example thresholds (2.5 g and 120 degrees/second) are placeholders for software demonstration, not validated crash thresholds. The current rule checks both signals in the same sample; it does not yet use a time window, alert countdown, or cancel button.

### Run the host-side checks

Requires a C++11 compiler such as `g++`.

From the repository root (PowerShell or a POSIX-style shell):

```sh
g++ -std=c++11 -Wall -Wextra -pedantic -Ifirmware/include firmware/src/crash_detector.cpp tests/test_crash_detector.cpp -o test_crash_detector
./test_crash_detector
```

On PowerShell, run the output as `./test_crash_detector.exe`.

The checks use simulated values only. They verify the basic rule’s expected behavior for ordinary movement, isolated acceleration, isolated rotation, a combined event, and values just below thresholds. Passing these checks does not establish real-world crash-detection accuracy.

### Hardware wiring and setup

Not provided yet. Add a verified wiring diagram, pin table, board selection, library versions, power requirements, and setup steps only after checking the actual assembled circuit. The presentation’s component list is not a verified wiring diagram.

## Testing and current results

The included test program is prepared for host-side execution with synthetic inputs, but it has not been successfully run in this environment: the available MinGW compiler failed to start because of a Windows DLL relocation error. No helmet hardware was connected. Run the command above with a working C++11 compiler before reporting test results. Record the compiler and version when publishing results.

No physical sensor tests, GPS tests, SMS delivery tests, battery tests, false-alarm measurements, or missed-detection measurements are reported here. Do not describe this as a tested safety product. Do not perform crash tests with a person wearing the helmet.

## Safe next steps

1. Confirm which design is being built (the presentation lists Arduino Nano; later project notes mention an ESP32 revision) and document the actual parts.
2. Confirm real sensor units, sample rate, mounting location, and wiring before connecting sensor readings to this logic.
3. Add a time-window/state-machine rule and an alert countdown with a cancel button; test these first with simulated sequences.
4. When hardware is available, perform controlled bench tests on a padded fixture, not on a rider. Log input readings, candidate decisions, and failures.
5. Test GPS availability, GSM registration/SMS failures, and power loss separately. The SIM800L is a GSM/GPRS module, so verify compatible service in the intended test location.
6. Treat MQ-3 readings as alcohol-vapour sensing only unless a suitable measurement method is calibrated and validated. Do not claim that it determines impairment or prevents riding after drinking.
7. Consider data logging or ML only after collecting sufficient safe, consented data and comparing against a clearly defined baseline.

## Limitations

- Nothing is tested yet, so we don't make any performance claims.
- Detection will be threshold-based. It reacts to motion after it happens and doesn't predict anything.
- A dropped helmet could look like a crash, and a gentle fall could be missed.
- GPS needs a clear view of the sky and can take time to get a fix.
- The SIM800L only works on 2G, so it depends on the operator and the area.
- The MAX30102 is a hobby sensor, not a medical device, and it gets unreliable with movement.
- The Peltier module draws a lot of current and needs heat management. Whether it actually helps the rider is not known.
- The MQ-3 only gives a rough reading of alcohol vapour. It can't measure blood alcohol or tell if someone is fit to ride.
- It isn't certified or tested to any helmet or electronics standard.

More in [`docs/SAFETY_PRIVACY_LIMITATIONS.md`](docs/SAFETY_PRIVACY_LIMITATIONS.md).

## Safety and privacy

Don't cut, drill or glue anything into a certified helmet to fit the electronics. Location and heart-rate data are personal, so alerts should only go to people who agreed to get them, and nothing should be logged without the wearer's consent. Don't commit real phone numbers or location logs to this repo.

## Future work

These are plans, not existing features.

- Assemble the circuit and test the basic sensor-to-SMS flow
- Tune the thresholds and measure false alarms and missed triggers in safe bench tests
- Add a countdown and cancel button so accidental alerts can be stopped
- Test GPS, SMS, battery life and failure cases
- Check that 2G service is available where we test, and look at a newer module if not
- Try the MAX30102, SD logging and Peltier one at a time, with power and heat checks
- Check the MQ-3 carefully if we keep it
- Try machine learning only if we collect enough safe, consented data and it beats the threshold version
- Find a way to mount the electronics without modifying the helmet

More in [`docs/FUTURE_WORK.md`](docs/FUTURE_WORK.md).

## Presentation and media

The project was presented on 16 April 2026 at the "Arrive Alive" Road Safety Awareness Programme at Woxsen University, Sangareddy. According to the university's write-up, over 500 students, police officials and academic leaders attended, including Shri Mahesh Muralidhar Bhagwat, IPS (Additional DGP, Law & Order, Telangana), Pankaj Paritosh (Superintendent of Police, Sangareddy) and Ms. P. Pravinya, IAS (Collector & District Magistrate, Sangareddy).

It was covered by The Hindu: ["Woxen University students showcase smart safety helmet at road safety drive"](https://lnkd.in/gk8eguRM), The Hindu Bureau, 16 April 2026.

Event write-up: [Woxsen University news](https://woxsen.edu.in/news/woxsen-hosts-arrive-alive-road-safety-programme/)

## Project status

This is an unfinished student project, currently in phase 2 of 4. The repository has the design, notes and one bench photo. There is no code or test data yet, and we'll add them as the build goes on.
