# Safety, Privacy & Limitations Statement

## Intended use
Educational prototype for learning embedded systems. **Not** a certified safety device, medical device, breathalyser or protective equipment. It must not be relied on to summon help, and must never replace calling emergency services.

## Helmet integrity
- Do not cut, drill, or glue into a helmet shell or its energy-absorbing liner; this can weaken the helmet.
- Mount electronics only in ways the helmet manufacturer permits, and keep wires/hard parts from pressing on the head.
- Check the helmet-certification rules that apply in your region before attaching anything.

## Electrical safety
- Use protected Li-ion cells and a proper charger circuit; keep batteries away from heat and protect from punctures.
- Insulate all wiring; keep the electronics enclosure away from moisture.

## Alcohol sensor
The MQ-3 gives an uncalibrated indication of alcohol vapour. It cannot measure blood-alcohol, determine impairment, or stop anyone from riding. No claim of preventing drunk driving is made.

## Privacy & consent
- GPS coordinates identify where a person is. Only send them to people the wearer has chosen, with the wearer's knowledge.
- Do not commit phone numbers, SIM numbers, API keys, or location logs to this repository.
- Any future data logging requires informed consent, local storage, minimal retention and a way to delete data.
- Do not add features that send location secretly. Any "silent SOS" idea needs a careful design for consent, visibility and misuse prevention before being built.

## Reliability limits
Detection is threshold-based and has not been validated. False alarms and missed events are expected until measured. GPS and 2G SMS can fail in many ordinary situations.

## No endorsement / no warranty
Provided as-is for learning. Use at your own risk.

## Phase 2 additions
- **Peltier cooling:** high current and a hot side that needs heat removal; keep it off a person until bench measurements show safe surface temperatures and current. Do not modify a helmet shell to fit it.
- **Heart-rate / SpO₂ (MAX30102):** not a medical device; readings must not be used for medical decisions or presented as "medical insight".
- **Health and location logs:** treat as sensitive personal data — informed consent, local storage, minimal retention, easy deletion.
- **Alerts to police or other third parties:** do not send test or automated messages to police or official numbers. Check with the relevant authority which channel (if any) accepts such alerts; for tests use team members' numbers with permission.
- **"Women's safety" / silent SOS use:** a hidden location-sending feature can also be misused to track someone. Any such feature needs consent, visible indication and an off switch before it is built.
