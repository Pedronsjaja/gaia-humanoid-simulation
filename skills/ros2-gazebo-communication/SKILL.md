---
name: ros2-gazebo-communication
description: Diagnose and implement reliable communication between ROS 2 Jazzy and Gazebo Harmonic/Gazebo Sim 8 using ros_gz_bridge or direct Gazebo Transport, including protobuf messages, contact sensors, timing, discovery, callbacks, and stale-data handling. Use when inspecting or changing a ROS/Gazebo topic path, sensor stream, bridge, or Python Transport subscriber in a simulation.
---

# ROS2-GAZEBO-COMMUNICATION

## Purpose and context

Use this skill to establish what is actually publishing, receiving, and being consumed across ROS 2 Jazzy and Gazebo Harmonic / Gazebo Sim 8. Treat Gazebo Sim 8.11.0 as the user's reported local version, not independently verified. Expect Python 3 and Gazebo Transport. Discover the active environment rather than assuming global paths; vendor paths may be under `/opt/ros/jazzy/opt/`.

Treat communication as an evidence problem. A declared sensor, topic, publisher endpoint, configured bridge, or controller rate does not prove that samples arrive or that a subscriber processes them. Report measurements with source, observation window, and uncertainty.

## Expertise and coordination

Apply practical ROS 2 and Gazebo Transport knowledge: topic discovery, protobuf schema/type matching, bridge directionality, asynchronous callbacks, ROS-side QoS, clocks/time domains, discovery partitions, and thread-safe handoff. Inspect the installed distribution's CLI help, Python bindings, package files, and documentation before relying on version-specific syntax or imports.

Coordinate server, world, plugin, sensor, and system-plugin setup with the Gazebo specialist. Coordinate timestamp, frequency, latency, jitter, and missing-sample analysis with the data-analysis specialist. Make handoffs concrete: include observed topic/type, commands, process/world identity, time window, and unresolved question.

## Safety and change control

- Begin with read-only inspection. Identify the exact world/server, process IDs, command lines, environment, partition, and topic endpoints before proposing interventions.
- Never terminate a `gz sim server` because multiple processes exist or a topic looks duplicated. Establish ownership and identity first; ask the user before stopping or restarting an ambiguous process.
- Make only minimal, reversible changes. Before any source, launch, world, plugin, or configuration edit, create a backup, show the proposed diff and rationale, and wait for explicit user authorization. Do not edit before authorization.
- Never alter OP3 geometry: preserve origins, axes, orientations, offsets, order, distances, and assembly. OP3 is an external reference only, not a source of physical GAIA measurements. Do not infer GAIA dimensions, dynamics, sensor calibration, forces, or physical properties from OP3 or unverified simulation output.

## Evidence-led workflow

### 1. Establish versions, environment, and process identity

Run read-only checks from the same shell/session used to start the simulation and subscriber:

```bash
command -v gz
gz sim --versions 2>/dev/null || gz sim --help | head -60
gz topic --help
ros2 --version
printenv | sort | grep -E '^(AMENT_PREFIX_PATH|COLCON_PREFIX_PATH|GZ_|IGN_|ROS_|RMW_|LD_LIBRARY_PATH|PYTHONPATH)=' || true
ps -eo pid,ppid,lstart,args | grep -E '[g]z sim|[g]z server|[g]z topic|[r]os_gz_bridge'
```

If a version option is unsupported, record that and inspect `gz sim --help` or installed package metadata; do not guess. Check installed ROS packages/executables with available package metadata and `ros2 pkg executables ros_gz_bridge`. When imports or libraries fail, inspect `/opt/ros/jazzy/opt/`, active `AMENT_PREFIX_PATH`, `PYTHONPATH`, and `LD_LIBRARY_PATH`; do not globally overwrite paths to hide an environment mismatch.

Record process PID/parent/command line/start time, selected world, launch environment, ROS domain, middleware, and Gazebo partition where available. Multiple Gazebo servers may be intentional. A topic name alone does not identify which server or partition a CLI or subscriber sees.

### 2. Inspect Gazebo endpoints and actual samples

List topics and inspect the live endpoint metadata:

```bash
gz topic -l
gz topic -i -t /world/<world>/model/<model>/link/<link>/sensor/<sensor>/contact
```

Replace the illustrative name with the observed full topic. Confirm the live message type and publisher/subscriber counts. Project contact topics are declared as `gz.msgs.Contacts`; verify against the live graph and installed schema. “Two publishers, no subscribers” is endpoint evidence only. It proves neither that either publisher emitted a sample nor which server/world owns the endpoints. Check for duplicate sensors, plugin instances, worlds, or partitions before interpreting duplicate publisher counts.

Observe actual samples for a bounded interval:

```bash
timeout 10s gz topic -e -t /<observed/topic>
```

If the installed CLI uses different options, consult `gz topic --help`. Preserve emitted timestamps and payloads. An advertised publisher is not proof of a received sample; a declared contact sensor is not proof of active sensing. No sample is not force zero. Represent absence as `UNKNOWN`, with last-received time and stale/timeout status.

### 3. Verify ROS endpoints and the bridge independently

Inspect the ROS graph and target topic from the active ROS environment:

```bash
ros2 topic list -t
ros2 topic info -v /<ros_topic>
ros2 topic echo /<ros_topic>
ros2 topic hz /<ros_topic>
```

Use `ros2 topic info -v` to check ROS publisher/subscriber endpoint counts and offered/requested QoS. `echo` verifies received messages; `hz` estimates observed ROS arrival rate, not Gazebo acquisition frequency. Record the observation duration and absence of messages. Compare graph views only after checking shell, ROS domain, and middleware.

For `ros_gz_bridge`, verify the bridge process and its Gazebo partition and ROS domain. Check installed executables and help rather than copying syntax from another release:

```bash
ros2 pkg executables ros_gz_bridge
ros2 run ros_gz_bridge parameter_bridge --help
```

Verify the configured Gazebo topic, ROS topic, direction, both message types, and installed conversion support against live endpoints. Similar message names do not prove compatibility. Check bridge logs and both sides independently: Gazebo samples, bridge endpoint, and ROS samples. A configured or running bridge alone proves neither successful conversion nor delivery.

### 4. Confirm protobuf type and payload semantics

Gazebo Transport topics carry Gazebo message types, typically protobuf. Confirm the exact fully qualified type (for example, `gz.msgs.Contacts`) and locate the matching installed `.proto`, generated descriptors, or versioned documentation. Inspect the schema before reading fields, assuming units, interpreting empty repeated fields, or mapping to ROS. Check whether timestamps are embedded and which clock they represent.

For contact data, distinguish topic/type declaration, advertised publishers, successfully received protobuf samples, payload content indicating contacts, and any downstream force calculation. Do not map absent messages, an empty payload, or absent contact entries to physical force zero unless verified schema and sensor semantics establish that interpretation for that sample. Preserve raw sample evidence before deriving metrics.

### 5. Implement or inspect direct Gazebo Transport Python subscribers

Do not invent Python module names, class names, method signatures, callback conventions, or lifecycle calls. First verify the installed Gazebo Transport Python bindings and versioned API documentation in the active environment. Some installations expose bindings only under vendor paths such as `/opt/ros/jazzy/opt/`; verify interpreter, import path, shared-library path, and package version. Test imports in a short read-only probe and compare with local docs/examples. If bindings or docs are absent, use the CLI for diagnostics or a supported bridge rather than guessing.

Use this language-neutral pseudocode as a design pattern, not as an API claim:

```text
verify installed Transport binding and documented API
create and retain a long-lived node/subscriber owner
subscribe to exact topic using verified protobuf message type
callback(sample):
    capture local monotonic arrival time immediately
    extract source/acquisition timestamp only if present and validated
    validate payload; copy needed immutable fields
    enqueue compact record for control/analysis thread
    return promptly
keep subscriber, callback, node, and owning objects alive
run the documented event loop/spin mechanism for the required lifetime
on shutdown, stop the event loop and release resources per installed docs
```

Ensure the callback remains referenced and subscriber/node objects are not garbage-collected while active. Do not assume construction starts callbacks or that a particular blocking spin method exists. Keep callbacks short and exception-safe: catch/log expected decode and validation errors at a controlled rate, preserve last error/receive time, and do not silently swallow all exceptions. Avoid crashing the application or blocking simulation/control threads.

Treat callbacks as potentially concurrent unless the installed API guarantees otherwise. Protect shared mutable state with a lock or thread-safe queue. Avoid holding locks during logging, control calculations, or I/O. Keep Transport callbacks separate from control-loop state updates; define ownership and shutdown order. Avoid high-volume per-sample logging that perturbs timing.

A ROS 2 Python subscriber uses `rclpy` and generated ROS message types, distinct from Gazebo protobuf types. Follow installed ROS Jazzy documentation for node, subscription, executor/spinning, and destruction APIs. Configure QoS only on ROS endpoints. Do not describe Gazebo Transport delivery as ROS QoS or assume ROS QoS fixes Gazebo discovery, protobuf incompatibility, or a broken bridge.

### 6. Measure acquisition, arrival, and latency separately

For each received sample, record (when available): source/acquisition timestamp from a verified schema and clock; local monotonic callback-arrival timestamp; sequence number or validated identity; topic/type; source process/world/partition; callback status.

Compute acquisition intervals from consecutive source timestamps only when their clock domain is valid and consistent. Compute arrival intervals from local monotonic receive timestamps. Estimate delivery latency only when source and receive clocks are meaningfully synchronized or their offset is validated; otherwise report timelines separately. Report sample count, observation duration, min/median/percentile/max intervals, gaps, and dropped/repeated/out-of-order samples when evidence permits. Never label control-loop iteration rate as sensor acquisition rate. Control rate and sensor acquisition rate are independent; neither implies the other. Bridge and callback load may also affect observed arrival timing.

Choose stale timeout from intended/observed acquisition behavior and explicit system requirements, not a universal arbitrary value. Expose `last_received`, age, timeout threshold, and a state such as `OK`, `STALE`, `UNKNOWN`, or `ERROR`. Before any verified sample, report `UNKNOWN`; after a previously valid sample exceeds its threshold, report `STALE`. Never substitute zero for absence. Preserve gaps in metrics and clear stale status only after fresh valid samples resume.

### 7. Check time domains and synchronization

Identify Gazebo simulation time, ROS time, and host monotonic/wall time separately. Check ROS simulated-time configuration and `/clock` when relevant:

```bash
ros2 topic info -v /clock
ros2 topic echo /clock
ros2 param get /<node_name> use_sim_time
```

Record absence when a topic/node does not exist. Simulation time may pause, reset, or run at a different rate from wall time. Do not compare timestamps across domains or compute latency without a known mapping. Use local monotonic time for callback-arrival intervals and timeout age. Handle timestamp rollback explicitly. A `/clock` bridge does not prove that every message timestamp is synchronized.

### 8. Diagnose discovery, partitions, and duplicate/stale endpoints

Compare environment and identity for the Gazebo CLI, simulation server, bridge, and direct Transport subscriber. Check `GZ_PARTITION` (and legacy `IGN_PARTITION` only when used by the installed release), ROS domain, middleware, network/container boundaries, and launch-time environment. Discovery is scoped: mismatched partitions/domains or network visibility can hide processes even when topic strings match.

Run `gz topic -l` and `gz topic -i` in the simulation environment, then in the bridge/subscriber environment when relevant. Correlate endpoint counts and sample observations with process identity and time. A stale endpoint may remain visible without useful samples; an observed sample may come from a different server than the one under investigation. Do not clean up processes or change partitions until identity is established and the user authorizes disruptive action.

### 9. Handle disconnection, stale data, and recovery

Distinguish transport errors, conversion failures, callback exceptions, no initial sample, and timeout. Retain the most recent valid sample and receive time but mark it stale after the chosen threshold. Do not repeatedly consume stale contact state as current. Make reconnection behavior explicit and use only documented mechanisms; verify recovery by observing fresh valid samples rather than assuming a restart fixed the issue. Use bounded retries/backoff if supported, surface persistent failures, and avoid spawning duplicate bridges/subscribers on each retry.

When a stream is missing, test in order: correct process/world/partition; live Gazebo topic/type; actual Gazebo samples; bridge or direct subscriber endpoint; conversion/decoder success; ROS graph and QoS if ROS is involved; callback execution; freshness and timestamp domain. Change one variable at a time and retain evidence.

## Reporting checklist

State observation window and environment. Separate advertised endpoints from received samples, and Gazebo evidence from ROS evidence. Include topic names/types, publisher/subscriber counts, process/world/partition identity, bridge direction/mapping, last received sample and timestamps, measured acquisition and arrival rates (or why either cannot be computed), freshness state, and errors. Mark unsupported inferences and unresolved identity/type/time-domain questions. Propose the smallest reversible next step; for source/config changes, show the backup plan and diff and wait for explicit authorization before editing.