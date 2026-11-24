---
name: humanoid-locomotion-engineer
description: Diagnose and improve OP3-reference humanoid walking and balance in ROS 2 Jazzy with Gazebo Harmonic / Sim 8. Use when investigating gait stability, trunk or foot motion, joint tracking, contact, ZMP/COP, trajectory timing, or locomotion-control tuning; preserve all original OP3 kinematic geometry and require explicit authorization before source or model changes.
---

# HUMANOID-LOCOMOTION-ENGINEER

## Mission and operating context

Use this skill to investigate simulated humanoid locomotion with evidence-first, reversible experiments. The project context is an OP3-reference humanoid in ROS 2 Jazzy and Gazebo Harmonic / Sim 8 (reported runtime 8.11.0), with 12 leg joints: six per side. The original ROBOTIS OP3 kinematic geometry is immutable in this workflow.

Treat project observations as reported evidence, not universal facts or proof of root cause:

- A prior run reportedly completed 10 walking cycles without falling, with a notable final oscillation.
- Reported trunk motion: X amplitude about 37.3 mm, Y about 8.5 mm, Z about 3.4 mm, roll about 2.4 degrees, and pitch about 9.2 degrees; pitch reportedly reached about 10.5 degrees during a transition.
- Reported trunk height decreased from about 0.279 m to about 0.265 m.
- Joint-reference tracking errors were typically below 1 degree. Do not assume actuator tracking is the root cause; good tracking does not establish balance.
- Contact sensor data are unverified. A declared topic or sensor in a model is not evidence that valid contact messages are published or that physical contact is correct.

Keep the external OP3 reference model distinct from GAIA's measured geometry and dynamics. It is a reference, not a validated GAIA model. A fixed-base laboratory run can help test components but does not demonstrate free walking or balance. Mark physical actuator, contact, and balance validation **NOT VALIDATED** unless actual authorized physical tests provide evidence.

## When to activate

Activate for gait instability, falls, drift, oscillation, foot scuffing, unexpected height or trunk motion, support transitions, contact anomalies, timing/synchronization faults, trajectory review, or evaluation of balance metrics. Also activate before proposing locomotion-related control, dynamics, contact, or simulation parameter changes.

Coordinate with the project's kinematics, Gazebo, communications, and data-analysis skills when present. Ask the kinematics skill to verify frame and transform assumptions; the Gazebo skill to inspect simulator and contact configuration; the communications skill to establish topic, QoS, and timing validity; and the data-analysis skill to process recorded measurements. Do not let collaboration override geometry or authorization safeguards in this skill.

## Evidence and classification

Before diagnosing, collect evidence and label each statement:

- **SIMULATED** — directly observed in the identified simulator run, with model/configuration, world, software/runtime, and simulation-time context.
- **REAL** — directly measured on physical hardware, with test conditions and instrumentation stated.
- **UNKNOWN** — not observed, unverified, inferred without adequate inputs, stale, or ambiguous.

Never promote a SIMULATED result to REAL. Never convert UNKNOWN contact, force, velocity, or sensor values to zero. Separate observed values, derived metrics, hypotheses, and proposed tests in notes and reports.

Record, where available: repository revision and uncommitted diff; model/world/controller/configuration identifiers; ROS distribution and package versions; Gazebo Harmonic / Sim version; fixed- or free-base condition; real or simulated status; physics step and controller/update rates; simulation clock behavior; command and sensor topic names and message types; QoS; timestamps; run duration; initial conditions; gait phase; and reproducible launch/recording commands.

Before attributing cause, check whether the necessary signals exist and are fresh, synchronized, correctly framed, correctly signed, in expected units, and sampled fast enough for the phenomenon. Inspect topic discovery, type, publisher/subscriber counts, QoS compatibility, message timestamps, and actual message contents. A topic declaration, successful launch, or plotted empty series is not positive evidence.

## Non-negotiable geometry and change-control rules

Do not change the original OP3 kinematic geometry to fix walking. Never alter any original joint positions, axes or orientations, offsets, distances, sequence, or assembly. Do not “correct” the reference geometry based on an assumed GAIA measurement. Prioritize evidence-supported trajectory, controller, dynamics, contact, synchronization, and physics investigation instead.

Before any source or model change, including a proposed model or configuration edit:

1. Make a pre-edit backup or a version-control checkpoint sufficient to restore the exact baseline.
2. Prepare a written proposal stating the evidence, hypothesis, precise files/parameters to change, expected effect, risks, and validation plan; show a diff before applying it.
3. Obtain explicit user authorization for that specific change.
4. Change only the authorized scope, preserve the backup, and compare results against the baseline.

Do not interpret a request to diagnose or recommend as authorization to edit. If authorization is absent, provide a proposed diff or parameter plan without applying it. Stop if a change would violate the geometry invariant.

## Diagnostic sequence

Follow this order; do not jump to gain tuning before validating signals, frames, trajectory, and phase.

### 1. Establish frames, signs, units, and support phase

- Identify world, trunk/base, pelvis, left/right foot, joint, sensor, and trajectory-reference frames from actual model and message definitions. Record transform direction and frame IDs; inspect TF rather than guessing.
- State axis convention, handedness, positive rotation direction, angle units, length units, and whether reported quantities are positions, deltas, amplitudes, peak-to-peak values, or RMS values.
- Establish the support sequence and phase timing from commanded gait and observed contacts. Define stance, swing, double support, touchdown, liftoff, and transition intervals explicitly. Do not infer contact phase from a gait clock alone.
- Verify simulation time versus wall time and ensure streams can be aligned. Detect pauses, resets, dropped samples, timestamp jumps, and asynchronous command/measurement data.

### 2. Validate trajectories before judging control response

- Inspect position, velocity, and acceleration references and measured joint states over complete cycles and transitions.
- Check continuity at segment boundaries, periodic wrap, stance-to-swing transitions, and initial/final conditions. Look for position jumps, velocity/acceleration discontinuities, unintended endpoint drift, and abrupt phase or frequency changes.
- Verify time parametrization, units, controller update cadence, interpolation method, command freshness, and synchronization across all 12 leg joints. Check that the chosen trajectory remains feasible under the actual time scale; do not assume a valid shape remains valid when sped up.
- Compare commanded and measured states with aligned timestamps and equivalent units. Report tracking error by joint, phase, and time, not only one aggregate maximum.
- Treat reference tracking and balance as different questions: small joint error shows that joints follow their references; it does not show that the reference keeps the body dynamically stable, that feet have sound contact, or that the center of mass is controlled.

### 3. Inspect support, swing clearance, impacts, and slip

- Determine the support polygon from verified foot-ground contact geometry for the current phase. Account for one-foot support versus double support, foot dimensions, and the actual contact patch; do not substitute a nominal stance polygon when the foot is not in contact.
- Check swing-foot clearance throughout the path, including toe/heel clearance, scuffing, penetration, and touchdown location and velocity. Inspect impact timing and post-impact transients.
- Evaluate slip only from synchronized foot motion relative to the ground during verified contact, with suitable frame and surface-velocity information. Distinguish foot motion in world coordinates from body-relative motion.
- Validate contact sensor messages, collision pairs, contact surfaces, friction, solver/contact configuration, and timestamp/QoS behavior before using contact data. A missing or invalid contact stream is **UNKNOWN**, never zero force or no contact.

### 4. Use COM, ZMP, and COP only with adequate inputs

- Compute center of mass (COM) only from the actual model mass/inertia distribution and correctly transformed segment poses, or use a verified simulator/estimator output with its assumptions stated.
- Compute or interpret ZMP only when the required whole-body dynamics and external/contact wrench information are available and correctly synchronized and framed. State the model assumptions and validity interval, especially around impacts, flight, multiple contacts, and non-flat or compliant terrain.
- Interpret center of pressure (COP) only from a valid contact-wrench or pressure measurement over an identified support surface. Identify sign conventions, contact patch, reference frame, and sensor calibration/physics validity.
- Do not claim ZMP or COP is available merely from joint positions. Joint angles alone do not provide ground reaction forces or contact wrenches; position-only kinematics cannot establish the actual external forces needed to infer these quantities. A model-based estimate requires additional valid inputs and assumptions, such as mass/inertia, base and segment kinematics, accelerations, gravity, and contact/external-force treatment. Label estimates as estimates, not measurements.
- Compare ZMP/COP with the appropriate verified support region only during intervals where the estimator and contact assumptions hold. Do not use a plot or scalar without its input provenance and limitations.

### 5. Correlate body motion, tracking, contact, and phase

Plot or tabulate synchronized time histories by gait phase for:

- trunk/base pose and velocity, including height, roll, pitch, and yaw;
- both foot poses and velocities, clearance, touchdown/liftoff, and ground-relative motion;
- all 12 leg-joint references, measurements, velocities, and tracking errors;
- verified contact state and available contact forces/wrenches;
- gait phase, support mode, commands, controller state, and simulation clock;
- angular momentum or a clearly named, correctly sourced proxy/estimate when valid.

Correlate oscillation onset with support transitions, impacts, phase errors, saturation, command discontinuities, contact loss/slip, or body-motion trends. Do not diagnose from a single peak or from joint error alone. When contact, force, angular momentum, or base-state inputs are absent or invalid, say **UNKNOWN** and specify the measurement needed.

### 6. Form hypotheses and run controlled, reversible tests

- Rank hypotheses by evidence and propose a discriminating test for each. First eliminate frame, sign, unit, timing, data-quality, and trajectory defects.
- Establish a reproducible baseline and retain the exact baseline configuration and results. Change one parameter class at a time—for example, trajectory timing/shape, controller gains, synchronization, contact/dynamics settings, or physics settings. Do not simultaneously tune multiple classes.
- Choose a bounded parameter sweep only when each trial is reproducible and safe; keep initial conditions, world, simulation settings, and metrics fixed. Identify the parameter value, run duration, trial count, and failure criteria.
- Use short guarded trials before longer cycles. Define objective stop conditions such as fall, excessive body excursion, invalid/missing critical signals, or unexpected contact. Do not continue a failed trial merely to reach a target cycle count.
- Compare each result with baseline using the same time/phase alignment and metrics. Keep a rollback path, revert a change that worsens safety or the target metric, and do not stack unverified fixes.
- After simulation improvements, report physical validation separately as **NOT VALIDATED** until authorized hardware evidence exists.

## Suggested diagnostics

Use actual discovered names and message types; placeholders below are not claims about project topic names. Save the exact commands and output context. ROS topic tools can help establish whether data are actually present:

```bash
# Discover topics, types, and nodes; do not assume a declared topic publishes data.
ros2 topic list -t
ros2 node list
ros2 topic info -v <actual-topic>
ros2 topic hz <actual-topic>
ros2 topic echo --once <actual-topic>

# Inspect transforms and clock only after identifying the applicable frames/topics.
ros2 run tf2_ros tf2_echo <parent-frame> <child-frame>
ros2 topic echo --once <actual-clock-topic>

# Record only the verified topics required for the hypothesis, plus clock/TF as needed.
ros2 bag record -o <baseline-or-trial-name> <verified-topic-1> <verified-topic-2>
```

A ROS 2 command may return no message because of absent publishers, incompatible QoS, paused simulation, wrong topic/type, or other faults. Preserve that as a diagnostic finding and investigate; do not fill gaps with zeros. Check simulator and controller logs for update-rate, reset, pause, warning, and error evidence. Do not modify launch, model, world, or controller files without the change-control steps above.

Use a phase-aligned analysis loop such as the following pseudocode; adapt names to verified data and keep invalid quantities unknown:

```text
for each trial:
    validate metadata, clock, frames, units, and stream freshness
    align reference, joint state, base/foot state, phase, and contact by timestamp
    mark missing, stale, or incompatible streams UNKNOWN; never replace with zero
    segment data into verified support and transition phases
    compute joint tracking error only for valid aligned reference/state pairs
    compute COM/ZMP/COP only when their model, wrench, and contact inputs are valid
    summarize body/foot excursions, clearance, impact/slip evidence, and metrics by phase
    compare trial with unchanged baseline; record hypothesis, single changed class, and rollback
```

## Reporting standard

Provide a concise traceable report containing the run classification (**SIMULATED**, **REAL**, or **UNKNOWN** per evidence), setup and revision, validated inputs, frame/sign/unit conventions, support-phase definitions, measured versus estimated quantities, and missing signals. Separate:

1. **Observations** — sourced values and intervals, with units and method.
2. **Interpretation** — hypotheses and confidence, including competing explanations.
3. **Limitations** — invalid, unavailable, or assumed data; explicitly mark unverified contact as **UNKNOWN**.
4. **Next test** — one discriminating, reversible test with baseline, parameter class, metrics, stop conditions, and rollback.
5. **Change status** — no edits made, or the explicit authorization, backup, approved diff, and outcome.

Preserve the geometry invariant in every recommendation. Never present fixed-base testing, reference-model behavior, declared sensors, or successful reference tracking as proof of GAIA walking balance or physical validation.
