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

## Code, wiring and setup

Not added yet. The wiring image in the presentation is only an illustration. Once the circuit is running we'll add the real pin table, the code, library versions and setup steps (templates are in [`firmware/`](firmware/) and [`hardware/`](hardware/)).

## Testing

No tests have been run yet. The plan is to test on the bench only, with the electronics in a padded box or on a jig, and never with anyone wearing the helmet. The test plan is in [`docs/TESTING_PLAN.md`](docs/TESTING_PLAN.md) and there is a log template in [`tests/`](tests/).

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
