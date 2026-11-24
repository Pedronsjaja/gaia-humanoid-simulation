---
name: robotics-data-analysis
description: Analyze and validate robotics simulation or sensor datasets with Python, pandas, NumPy, Matplotlib, and CSV. Use when inspecting ROS 2 Jazzy/Gazebo Sim 8 controller or simulation outputs, trajectories, timing, contact topics, or locomotion diagnostics; distinguish simulated evidence from real-robot measurements and unknown data.
---

# ROBOTICS-DATA-ANALYSIS

## Apply when active

- Treat the current diagnostic context as a **10-cycle simulated OP3 walk** with reported trunk oscillation and pitch, an output CSV trajectory, a simulation log, and foot-contact topics declared but with actual message flow unconfirmed.
- Treat OP3 as an external reference only. Do not represent its geometry, mass, inertia, actuator behavior, or dynamics as measured GAIA properties. Preserve OP3 geometry when inspecting or discussing reference assets; do not alter or replace it.
- Treat the repository's fixed-base workflow as fixed-base only. Do not claim that it validates walking, balance, contact, or locomotion.
- Label every result as **SIMULATED**, **REAL**, or **UNKNOWN/NOT_MEASURED**. Keep simulation-derived observations separate from real-robot measurements; never claim that a simulation experiment proves physical robot behavior.
- Coordinate timing questions with communications (source timestamps, arrival timestamps, transport and sampling) and coordinate gait-state/phase interpretations with locomotion. Do not assign gait phase from a waveform alone without the locomotion state definition.

## Required workflow

1. **Establish provenance before analysis.** Record dataset path/name, source system and whether it is SIMULATED or REAL, run/session ID, collection date, ROS 2 distribution and simulator/version when known, controller/configuration revision, command and topic names, logger/export path, timebase, timestamp semantics, declared units, coordinate frames, and relevant parameters. Identify unknowns as UNKNOWN/NOT_MEASURED rather than filling them in.
2. **Inspect files and schema read-only.** List CSV headers, row count, data types, sample rows, timestamp columns and units. Compare the schema with the producer or logging code where available. Do not silently rename ambiguous columns, infer units from values, reorder coordinates, or overwrite source data.
3. **Validate before computing.** Check required columns, parseability, missing and duplicate rows, NaN/inf values, monotonicity, timestamp duplicates, ranges, outliers, units, coordinate frames, and radians-versus-degrees. Record every assumption and exclusion. Preserve an untouched input and write derived data/results separately.
4. **Establish clock semantics and observed timing.** Identify whether timestamps represent simulation time, device/source acquisition time, host monotonic arrival time, or wall-clock time. Keep simulation time separate from host monotonic time. Do not subtract timestamps from unrelated clocks without a documented clock mapping or synchronization method.
5. **Quantify observed timing from actual acquisition timestamps.** Report inter-sample interval (`dt`) distribution, observed acquisition frequency, jitter, gaps, and—only if both valid source and arrival timestamps exist—latency. State how jitter and gaps are defined, the selected timestamp column, timebase, and any filtering. Distinguish acquisition time from message arrival and processing time.
6. **Interpret conservatively.** Separate observation from inference, distinguish valid zero-valued measurements from missing data, state limitations, and avoid mechanics claims unsupported by measured inputs. Do not infer force, center of pressure (COP), or zero-moment point (ZMP) without validated force measurements, geometry, coordinate frames, and reference conventions.
7. **Make analysis reproducible.** Preserve the original CSV/log. Record analysis code/version, parameters, filters, units, exclusions, timestamp choice, figures, and outputs. Do not modify source, controller, or configuration files unless the user explicitly authorizes the change after reviewing a written proposed diff; make a pre-edit backup before any authorized edit.

## CSV and timestamp checks

Use pandas/NumPy/Matplotlib and adapt column names only after confirming the producer schema. Never treat an example column name as guaranteed. For example, after setting the actual path and confirmed column names:

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

path = "trajectory.csv"
time_col = "timestamp"       # replace with confirmed source/acquisition timestamp
value_cols = ["trunk_pitch"]  # replace with confirmed signal columns
expected_units = {"timestamp": "s", "trunk_pitch": "rad"}  # verify, do not assume

df = pd.read_csv(path)
print("rows:", len(df), "columns:", df.columns.tolist())
print(df.dtypes)
print(df.head())

required = [time_col, *value_cols]
missing_cols = sorted(set(required) - set(df.columns))
if missing_cols:
    raise ValueError(f"Missing required columns: {missing_cols}")

# Preserve original row indices so exclusions are auditable.
df["_source_row"] = np.arange(len(df))
for col in required:
    df[col] = pd.to_numeric(df[col], errors="coerce")

print("exact duplicate rows:", int(df.duplicated(subset=required, keep=False).sum()))
print("duplicate timestamps:", int(df[time_col].duplicated(keep=False).sum()))
print("missing/non-finite by column:\n", (~np.isfinite(df[required])).sum())

valid = np.isfinite(df[required]).all(axis=1)
clean = df.loc[valid].copy()  # report exclusions; do not silently discard them
if len(clean) < 2:
    raise ValueError("Need at least two valid timestamped samples")

# Check monotonicity in original valid-row order before any sorting.
t = clean[time_col].to_numpy(dtype=float)
dt = np.diff(t)
print("strictly increasing:", bool(np.all(dt > 0)))
print("non-positive dt count:", int(np.sum(dt <= 0)))

# Do not sort away clock resets, out-of-order acquisition, or duplicated times.
# Resolve their meaning from producer semantics before selecting a segment.
pos_dt = dt[dt > 0]
if pos_dt.size:
    # Nominal observed rate is based on actual positive timestamp intervals.
    # The median is robust to occasional gaps; it is not the configured rate.
    rate = 1.0 / pos_dt
    med_dt = float(np.median(pos_dt))
    jitter = pos_dt - med_dt  # explicitly defined here as deviation from median dt
    print("positive dt (s), p50/p95/p99/max:",
          np.percentile(pos_dt, [50, 95, 99, 100]))
    print("observed rate (Hz), p50/p05/p01/min:",
          np.percentile(rate, [50, 5, 1, 0]))
    print("jitter = dt - median(dt), s; abs p50/p95/p99:",
          np.percentile(np.abs(jitter), [50, 95, 99]))

    # Define a gap threshold explicitly. Replace this example rule if the
    # acquisition specification defines another threshold; report the rule.
    gap_threshold_s = 2.0 * med_dt
    gap_idx = np.flatnonzero(dt > gap_threshold_s)
    print(f"gaps where dt > 2*median_dt ({gap_threshold_s:g} s):", len(gap_idx))
    print("gap intervals (s):", dt[gap_idx])

# Plot only after validating the signal's units and timestamp meaning.
fig, ax = plt.subplots(figsize=(10, 4))
for col in value_cols:
    ax.plot(clean[time_col], clean[col], label=f"{col} [{expected_units.get(col, 'unit UNKNOWN')}]")
ax.set_xlabel(f"{time_col} [{expected_units.get(time_col, 'unit UNKNOWN')}] — verified timebase required")
ax.set_ylabel("Signal (verify units and frame)")
ax.grid(True, alpha=0.3)
ax.legend()
fig.tight_layout()
plt.show()
```

Interpret the diagnostics carefully:

- A timestamp can be numeric and still have unknown units or semantics. Verify epoch, scale, reset behavior, frame, and whether it is source acquisition or logger arrival time.
- Keep rows in acquisition order when checking monotonicity. Sorting can conceal out-of-order delivery, resets, or corruption. Segment a documented reset only after identifying its cause.
- Report exact duplicate rows, duplicate timestamps, invalid rows, and exclusions independently. Investigate outliers against the log and sensor limits; do not automatically clip or remove them.
- The example's `2 × median dt` gap rule is a transparent diagnostic heuristic, not a universal threshold. Prefer producer/acquisition requirements when available. A loss rate requires an expected sample sequence/count or a defensible expected schedule; do not call gaps a packet-loss rate without that evidence.
- Compute latency only when source and arrival timestamps are both available and clock alignment/offset is validated. Report the timestamp pair, units, clock mapping, and latency distribution. Arrival intervals are not source acquisition intervals.
- For angle data, verify radians or degrees from the producer and metadata. Convert only in a separate, documented derived column. Verify whether values are wrapped or unwrapped before interpreting discontinuities.
- Verify coordinate frame, axis directions, origin, handedness, and pose convention for every position/orientation signal. Do not combine values from different frames without a documented transform.

## Rate, loss, and ROS 2 interpretation

Use ROS 2 introspection as corroborating evidence, not as a substitute for timestamped acquisition data. Example commands (adjust topic names and run while the process is active):

```bash
ros2 topic list -t
ros2 topic info -v /actual/topic
ros2 topic hz /actual/topic
ros2 topic echo /actual/topic --once
```

`ros2 topic hz` measures the rate observed by its subscriber during that observation window. It can be affected by QoS, executor load, transport, subscription behavior, and measurement duration; it does not by itself establish sensor acquisition rate. A topic's publisher rate or declared `update_rate` is not the observed acquisition rate. A controller command rate and a callback rate are also different quantities. Report these separately whenever known:

- configured sensor `update_rate`;
- publisher emission rate;
- controller command rate;
- callback/processing rate;
- observed source acquisition rate from acquisition timestamps;
- observed message-arrival rate from arrival timestamps.

For ROS 2 Jazzy with Gazebo Sim 8, identify whether records come from simulation time (`/clock` or simulation-stamped messages), host monotonic time, wall clock, or another clock. Record pause/reset behavior and time scaling. A simulation clock may pause or jump; a host monotonic clock should not be substituted for simulation time. For cross-topic synchronization, verify message timestamp conventions, clock source, offsets, and sampling windows. State interpolation/resampling rules and avoid implying simultaneous measurements when clocks or sampling are unsynchronized.

Only calculate a delivery/loss rate when the source offers sequence numbers, a known message count, or a defensible schedule and the observation window is defined. State the denominator, expected-sample method, and unobserved interval. Do not infer missing messages merely from a slower callback or a measured frequency below a nominal setting.

## Contact, gait, and mechanics safeguards

- A declared foot-contact topic is not evidence that it published, arrived, or contained valid samples. Inspect actual recorded messages/logs and timestamps. If flow cannot be confirmed, report **UNKNOWN/NOT_MEASURED** or “no valid sample observed in the inspected data,” with the inspected source/window.
- Never infer `force = 0` from an absent message, missing CSV row, inactive topic, or lack of a contact event. Distinguish a valid, timestamped zero-valued force sample from missing/invalid/unobserved force data.
- Distinguish contact state from force magnitude. Confirm the message schema, units, sign convention, filtering, sensor/physics source, coordinate frame, and validity flags before interpreting either.
- Do not derive force from contact state or derive COP/ZMP without validated force/torque data, sensor/contact geometry, reference frame, and computation conventions. Record all assumptions if the user authorizes a derived calculation.
- Interpret gait phase and cycle counts using locomotion's state definitions, event thresholds, and phase conventions. The stated 10-cycle OP3 walk is simulation context, not evidence of ten measured real-robot cycles or verified contacts.
- Describe trunk oscillation and pitch only as signals observed in the identified simulation output, with units, frame, filtering, and timebase specified. Do not diagnose physical instability or balance from that observation alone.

## Reporting and change control

For each result, include:

- **Evidence class:** SIMULATED, REAL, or UNKNOWN/NOT_MEASURED.
- Dataset/run provenance and source paths; schema and verified units/frames.
- Timestamp column, timestamp meaning/timebase, synchronization method, observation window, and whether intervals are source-based or arrival-based.
- Actual observed sampling statistics, jitter definition, gap threshold, and any valid loss-rate method; separate all nominal/configured rates from observed rates.
- Processing steps, code or notebook version, parameters, filters, exclusions, and treatment of duplicates, missing values, NaNs, outliers, and resets.
- Figures/tables with labeled units, coordinate frame, source, and timebase.
- Limitations and unknowns, including unconfirmed contact flow and lack of physical validation where applicable.

Keep conclusions proportional to evidence. For proposed source, controller, or configuration edits, first make a pre-edit backup, then provide a written proposed diff and obtain explicit user authorization before applying any change. Do not edit those files as part of analysis without that authorization.
