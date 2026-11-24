---
name: gazebo-sim-specialist
description: Diagnose, configure, and validate GAIA humanoid simulation in ROS 2 Jazzy with Gazebo Harmonic / Gazebo Sim 8, including SDF systems, contact sensors, Gazebo Transport, plugin loading, and walking stability. Use when investigating Gazebo runtime versions, missing or unreliable contact data, system or sensor setup, physics behavior, or simulation evidence. Require evidence before proposing changes, and never change reference OP3 kinematic geometry.
---

# GAZEBO-SIM-SPECIALIST

## Mission and activation scope

Use this skill for diagnosing or configuring a humanoid simulation built with ROS 2 Jazzy and Gazebo Harmonic / Gazebo Sim 8. Apply it to Gazebo Sim version and installation checks, SDF/world/model placement, system plugins, contact sensors and collision binding, physics and contact generation, Gazebo Transport discovery and message flow, log/process/partition identification, and simulation-only walking observations.

Do not treat the supplied investigation notes as current machine state. Re-check every relevant executable, process, world, environment variable, log, plugin, topic and sensor at the time of diagnosis. The reported executable path, vendor library path, model name, topic examples, plugin configuration and walking outcomes are leads, not proof.

## Required knowledge and evidence rules

- Distinguish Gazebo Sim (the simulator) from Gazebo Transport (`gz-msgs`, `gz-transport`) and ROS 2 topics. A Gazebo topic does not become a ROS topic unless a bridge or other integration is present.
- Distinguish a topic being listed, a publisher being advertised, a message being produced, and a message being consumed. Treat these as separate observations.
- Treat missing, invalid, stale, unbound, or unverified contact data as **UNKNOWN**, never as zero force or proof of no contact. Do not infer contact from a topic declaration, publisher count, or sensor configuration alone.
- Keep conclusions within the evidence gathered. Identify command, machine/context, time, world, server/partition, and relevant output when feasible. Mark unverified claims explicitly.
- Treat the OP3 model as an external reference pinned in `simulation/op3.repos`, not as measured GAIA geometry or dynamics. Never claim OP3 masses, inertias, dimensions, friction, controller behavior, or performance apply to GAIA.
- Treat simulation results as simulation evidence, not physical validation or a guarantee of safe real-hardware behavior.
- Preserve the exact original OP3 kinematic geometry. Never change joint origins, axes, orientations, offsets, order, distances, or assembly to fix physics, contacts, balance, or walking.
- Prefer read-only inspection. Propose the minimum reversible edits only after evidence supports them. Back up first, show the proposed diff, and wait for explicit user authorization before editing any source, model, SDF, world, or configuration file.
- Do not stop or kill any Gazebo server until each process, its world, Transport partition, and topic source have been identified and the user has authorized stopping it. Never use broad `pkill`/`killall` as a diagnostic shortcut.

## Evidence-first diagnostic sequence

Follow the sequence below. Record the working directory and preserve command output when useful. Do not jump to model edits because a topic is quiet or a simulation oscillates.

### 1. Identify the runtime and installation

Check the actual active executable, version, environment, and ROS/Gazebo package context; do not assume an installation path:

```bash
date -Is
pwd
command -v gz || true
readlink -f "$(command -v gz)" 2>/dev/null || true
gz sim --versions
gz --versions 2>/dev/null || true
env | sort | grep -E '^(GZ|IGN|ROS|AMENT|LD_LIBRARY_PATH|PATH|HOME|USER)=' || true
ros2 doctor --report 2>&1 | head -200
```

Use `gz sim --versions` as the primary Gazebo Sim version check. If it fails, record the error and inspect shell `PATH`, ROS setup scripts and installed packages; do not silently substitute a different `gz` binary. In this project context the user's local runtime previously reported Gazebo Sim 8.11.0 via ROS vendor packages on Ubuntu 24.04, commonly at `/opt/ros/jazzy/opt/gz_tools_vendor/bin/gz`. Verify that path and version before using it. Avoid sourcing or changing the user's shell configuration as part of diagnosis unless explicitly authorized.

### 2. Identify the exact simulation inputs and world placement

Locate the actual launch command/process, selected world and model/SDF files. Inspect the world for the model include or insertion pose, the model's `<world>`/`<model>` structure, and whether the sensor-bearing links and collisions exist in the instantiated model. Confirm the model name and world name from the running simulation rather than assuming `default` or `robotis_op3`.

Read the relevant SDF snippets and report file paths and line numbers where possible. Check schema/version compatibility with the active simulator. Trace launch files, included SDFs, generated models and runtime resource paths to the concrete file loaded. Do not edit files while tracing them.

### 3. Confirm the system plugin declaration and load outcome

Inspect the world/model SDF for system plugin declarations. For each relevant `<plugin>` record both:

1. the exact shared-library filename in `filename="..."`; and
2. the exact registered system name in `name="..."`.

Do not conflate the library basename with the plugin's registered name. Check spelling, scope/placement in SDF, compatibility with the active Gazebo Sim major version, and duplicate declarations. A declared plugin is not proof that the server loaded or instantiated it.

Search active server logs and startup output for the plugin filename/name and load, symbol, ABI, dependency, or registration errors. Find candidate libraries without presuming the reported location is present:

```bash
ldconfig -p | grep -Ei 'gz-sim|contact-system|ignition-gazebo' || true
find /opt/ros /usr/lib /usr/local/lib -type f \\
  \( -iname '*contact*system*.so*' -o -iname '*gz-sim*contact*.so*' \) 2>/dev/null
```

For a candidate library, inspect dependencies and unresolved libraries:

```bash
file /path/to/candidate.so
ldd /path/to/candidate.so
```

Use the exact candidate path in place of the placeholder. If `ldd` reports `not found`, diagnose the missing dependency and search path before proposing any change. Check `GZ_SIM_SYSTEM_PLUGIN_PATH` (and version-appropriate documented equivalents) and the active process's environment; do not assume the interactive shell environment matches a running server. Verify with logs that the plugin loaded. The reported candidate `/opt/ros/jazzy/opt/gz_sim_vendor/lib/libgz-sim8-contact-system.so.8.11.0` is unconfirmed until checked on the target runtime.

### 4. Trace each contact sensor to a real collision

For each sensor, trace the SDF hierarchy and runtime entity association:

- confirm sensor type is `contact` and inspect update rate, topic, and collision/filter configuration;
- verify the referenced collision name resolves in the same model/link scope supported by that SDF form;
- confirm the collision is present, enabled, and participating in collision detection, and belongs to the intended foot link;
- check whether the model uses nested includes, renamed links, multiple collisions, fixed joints, or generated descriptions that change the effective entity tree;
- verify sensor and collision entities in the loaded simulation where tools permit, not only in a source file.

The provided SDF reportedly declares 100 Hz foot contact sensors. This is only configuration evidence: it does not prove the sensor is bound, the contact system is loaded, contact is generated, or a 100 Hz stream is being published.

### 5. Establish server, process, partition, and topic identity

Inventory Gazebo server processes without stopping them. Map each PID to its command line, world/input, executable, parent, start time, and relevant environment/partition. Multiple `gz sim` servers were previously observed; do not attribute a topic to the intended robot until its source server and partition are established.

```bash
pgrep -af 'gz sim|gzserver|ign gazebo' || true
ps -eo pid,ppid,lstart,stat,args --sort=pid | grep -E '[g]z sim|[g]zserver|[i]gn gazebo'
```

Inspect `/proc/<PID>/cmdline`, `/proc/<PID>/environ` (filter to relevant `GZ`, `IGN`, and resource-path variables), and `/proc/<PID>/cwd` where permitted. Treat environment variables such as `GZ_PARTITION` as process-specific. Record the active shell's partition too, but do not mistake it for the server's partition. Use a deliberately matched client environment or the appropriate CLI option for the installed Gazebo version only after verifying its supported syntax. Avoid exporting a new partition and then concluding the simulator has no topics.

List and inspect topics in the matching context:

```bash
gz topic -l
gz topic -i -t /world/default/model/robotis_op3/link/l_ank_roll_link/sensor/left_foot_contact/contact
gz topic -i -t /world/default/model/robotis_op3/link/r_ank_roll_link/sensor/right_foot_contact/contact
```

Treat these paths as examples only; discover actual world/model/link/sensor names first. Confirm the reported type is `gz.msgs.Contacts`. If a CLI flag or topic command differs in the installed release, consult that executable's `--help` and use its documented syntax. Do not force the example path onto a differently named instance.

### 6. Separate topic metadata from observed message flow

Capture topic metadata and actual samples separately. The topic's existence or a listed publisher proves advertisement/discovery, not messages flowing:

```bash
gz topic -i -t /actual/contact/topic
gz topic -e -t /actual/contact/topic
```

Use a bounded observation window or timeout when appropriate (for example, wrap echo with `timeout 10s`), and save the exact output/errors. Observe under a controlled known-contact condition and a known no-contact condition only when safe and meaningful. Confirm sample timestamps, changing content, contact/collision identity and plausible force/normal data according to the message schema. Do not interpret a successful subscription with no samples as zero contact. Do not use message echo as proof that the data is fresh or correctly bound without examining message contents and timing.

A prior inspection reportedly showed two publishers on the left topic. Re-check it. Multiple publishers make source attribution more important, not less; identify which server/partition/model each publisher belongs to. Never average, merge, or choose publishers without identifying them.

### 7. Check physics and logs before changing configuration

Inspect the active world's physics configuration (engine, step size, real-time update rate, solver/contact settings), the model collision surfaces and contact parameters, and relevant system plugin configuration. Compare simulated contact occurrence with actual collision geometry and motion. Check that the server is stepping and not paused, stalled, or reporting errors. Do not tune friction, solver values, sensor rate, controller gains, or model geometry as a first response to an unverified sensor stream.

Locate logs from the exact process and launch path. Check the terminal that started it, launch/supervisor output, and applicable user/system journal entries. Search narrowly for plugin, sensor, collision, transport, physics, SDF, and error/warning messages. State when logs are unavailable; do not infer a successful load from silence in an unrelated terminal.

For motion claims, report the actual run duration, cycle count, falls, trunk pitch measurement method and reference frame, and end-of-run oscillation evidence. The current investigation notes report 10 OP3 walking cycles without falling, noticeable end oscillation, and roughly 9–10 degrees trunk pitch. Preserve this as a reported observation until the run, controller, coordinate convention, measurement, and model identity are verified. A visually estimated pitch is not a measured time series.

## Interpretation and escalation

Use the following evidence ladder in reports:

1. **Configured** — SDF or launch file declares a sensor, topic, or plugin.
2. **Available** — expected library/executable exists and dependencies resolve.
3. **Loaded** — the intended server log/process confirms plugin or sensor initialization.
4. **Advertised** — Gazebo Transport metadata shows a topic and publisher in the identified partition/server context.
5. **Observed** — subscriber receives samples with valid content and timestamps.
6. **Validated** — controlled tests establish correct collision binding, contact behavior, freshness and plausible measurements.

Do not promote a conclusion above its evidence level. For a missing or invalid contact stream, report **UNKNOWN** and list the next test that can disambiguate plugin load, binding, physics generation, partition mismatch, or topic source.

Involve a **communication specialist** when the task concerns Gazebo Transport callback code, subscriber/publisher lifecycle, asynchronous callbacks, queueing, or middleware behavior. Involve a **data-analysis specialist** when measuring or comparing sample rate, jitter, latency, dropped messages, contact duration, force profiles, or walking/pitch time series. Provide them with identified source, timestamps, units, and raw samples rather than unsupported summaries.

## Change control and reporting

Only after read-only evidence isolates a likely cause:

1. Propose the smallest reversible change that addresses that cause; keep geometry and kinematic structure invariant.
2. Identify the exact file(s), intended effect, risks, and validation/rollback steps.
3. Before any edit, create a timestamped backup or version-control checkpoint and verify that it exists. Do not overwrite user changes.
4. Present the exact proposed diff and wait for explicit user authorization before editing any source/model/world/SDF/configuration file. Do not treat a request to diagnose as permission to edit.
5. After authorization and edit, verify the diff, perform the narrowest relevant simulation check, capture logs and topic samples, and report both outcome and remaining uncertainty. Roll back only with authorization or if rollback was explicitly included in the approved plan.

Give concise findings with the observed facts, their evidence level, uncertainties, and next safe action. Keep OP3-reference results distinct from GAIA claims, and state explicitly that simulation is not physical validation.
