# Validation by engineering layer

[Status](engineering-status.md) · [Historical evidence](simulacao/validacao.md)

Label every result TESTED AUTOMATICALLY, TESTED IN SIMULATION, TESTED ON HARDWARE, TESTED MANUALLY or NOT TESTED. Record revision, environment, command/procedure, expected criterion, observed result and limitations. Static inspection does not demonstrate runtime behavior; a mock does not measure a servo.

| Layer | Scope and evidence | Boundary |
| --- | --- | --- |
| Unit | OP3 adapter preservation/configuration; actuator parser, conversions and mocked serial failure cases | No physical motion or timing validation |
| Simulation | Gazebo launch, active controller, fresh sensors, frames, head out-and-back | No physical torque, balance or sensor calibration proof |
| Bench | One identified LX-225, fixture, power/stop checks, external measurement and retained raw data | Not whole-robot integration |
| Integration | ROS ↔ calibrated interface ↔ BusLinker; timing, ownership, stale-data/fault injection | Must be tested on hardware after mocked checks |
| Subsystem | Supported leg/arm with known axes/limits and load | Not unsupported standing |
| System | Suspended then restrained humanoid; independently reviewed stops and experiment envelope | Not autonomous walking by default |
| Digital Twin | Held-out real/virtual trajectories, time alignment, uncertainty and residuals | Only the validated operating domain is supported |

## Run existing adapter tests

In the built Ubuntu workspace, source ROS and `install/setup.bash`, then run from this repository:

```bash
python3 -m unittest discover -s ros2/gaia_op3_sim/test -v
```

Expected: five adapter tests pass against the imported OP3 description. They verify geometry/dynamics preservation, fixed/free support, expected joint/controller names, rejection of incompatible upstream input and sensor/bridge correspondence.

For a non-ROS Python environment with `xacro` and `PyYAML`, use the existing test override. Replace the external path with the description checkout pinned by `simulation/op3.repos`:

```bash
export PYTHONPATH="$PWD/ros2/gaia_op3_sim"
export OP3_DESCRIPTION_PATH="/absolute/path/ROBOTIS-OP3-Common/op3_description"
python3 -m unittest discover -s ros2/gaia_op3_sim/test -v
```

This uses a test-only Xacro package lookup override. It does not test ROS discovery, launch, package installation or Gazebo. Record Python/xacro/YAML versions when reproducing.

## Run existing simulation checks

The [workflow](../.github/workflows/simulation.yml) contains the Linux build/smoke recipe. Documentation-only changes do not automatically trigger its path-filtered jobs. In a prepared isolated Ubuntu lab, start `ros2 launch gaia_op3_sim lab.launch.py gui:=false`, then in another sourced terminal run:

```bash
python3 ros2/gaia_op3_sim/test/smoke_sim.py
```

Never run this command against hardware: it publishes head targets. Expected: five streams remain fresh, controller active, head reaches 0.15 rad and returns to zero within test tolerance. Keep `smoke-metrics.json` and simulator logs. A pass does not validate nominal rates, visual framing, free-base stability or walking. The [lab](simulacao/instalacao-e-laboratorio.md) adds manual visual checks.

## Actuation and mechanics

The actuator repository's safe mocked suite is `python -m unittest discover -s tests -v` from its root; do not substitute hardware-facing `test_servos.py`. The structure manifest can be hash-checked offline; this verifies file integrity, not dimensions, CAD assembly correctness or strength. See the [current audit](research/documentation-audit.md) for commands actually executed in this revision.

Before physical experiments, define setup, hypothesis, acquisition, uncertainty, pass/fail limits and abort procedure. Leave missing observations NOT_MEASURED. Preserve failed trials and raw data separately from derived plots.
