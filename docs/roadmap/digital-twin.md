# From virtual engineering to Digital Twin experiments

[Simulation-to-real gates](simulation-to-real.md) · [Actuator research roadmap](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/digital-twin-roadmap.md)

Current: an OP3 simulation/virtual engineering environment and a separate actuator bench stack. Future: experiments relating an identified physical GAIA system to a calibrated virtual counterpart. A moving simulation, a live dashboard or replaying the same command in two systems is insufficient evidence of a validated Digital Twin.

## Minimum experiment

Start with one identified physical joint. State the question (for example, prediction error across motion reversals), hypothesis, fixture, safety constraints, software/CAD/calibration revisions and independent position reference. Collect unmodified commands and feedback with units, receipt times, acquisition-time assumptions and quality flags. Driver receipt time is not necessarily the sensor acquisition instant.

Identify parameters using a training dataset; reserve different trajectories/load conditions for validation. Keep raw measurements separate from filtered/derived data. If feedback is internal to the servo, do not call it external ground truth. Report error versus time, bias, lag, repeatability, missing data and uncertainty; select acceptance limits before seeing validation results. Values are NOT_MEASURED until an experiment exists.

## Progression and evidence

| Stage | Deliverable | What it does not prove |
| --- | --- | --- |
| Virtual model | Explicit geometry/dynamics assumptions | Real mechanism accuracy |
| Physical acquisition | REAL observations, timestamps and calibration | Synchronization or model validity |
| Offline alignment | Clock relation, latency estimate and residuals | Live operation |
| Parameter identification | Reproducible fit with constraints | Generalization to unseen data |
| Independent validation | Held-out comparisons and validity domain | Whole-robot or all-load validity |
| Live synchronization | Bounded age/jitter, loss/restart handling | Safe closed-loop actuation |
| Controlled experiments | Reviewed model use and fail-safe behavior | Autonomous locomotion or universal fidelity |

The future channel contract must include name, unit, physical origin/sensor, nominal and measured rate, frame, acquisition/receipt time, quality, range and provenance (REAL/SIMULATED/CALCULATED/ESTIMATED). This schema is a requirement, not an implemented logging guarantee across all repositories.

Challenge: compare one trajectory using receipt time and then a measured clock/latency correction. Explain how the alignment uncertainty changes the apparent model error. Do this offline before any synchronized actuation.
