# Engineering status and evidence

[Home](../README.md) · [Audit](research/documentation-audit.md) · [Test strategy](test-strategy.md)

Audited 2026-10-26/27 from simulation `a3bc0e3`, actuation `61ec128`, structure `4e64962`. Implementation status and validation status are separate dimensions. IMPLEMENTED means code/assets exist; EXPERIMENTAL means scope/reliability is limited; PLANNED means no delivered integration; NOT VALIDATED means relevant evidence is absent; UNKNOWN means design/configuration is unresolved; NOT_MEASURED means no measurement is available. None is a hardware certification.

## SIMULATED / REAL / PLANNED matrix

| Component | Simulated | Physical | Integrated | Validated / evidence boundary |
| --- | --- | --- | --- | --- |
| Robot structure | IMPLEMENTED OP3 reference | GAIA CAD/support assets; final assembly UNKNOWN | GAIA URDF PLANNED | OP3 adapter tests and historical simulation evidence; GAIA mechanics NOT VALIDATED |
| LX-225 | No characterized LX-225 model; ideal OP3 position joints only | Bench driver IMPLEMENTED; current connected hardware NOT VERIFIED | ROS/BusLinker interface PLANNED | Mock serial tests; physical performance NOT_MEASURED in this audit |
| Camera | IMPLEMENTED ideal teaching camera | Installed GAIA camera UNKNOWN | Gazebo → ROS IMPLEMENTED | Historical smoke checks message/frame/width; vision quality NOT VALIDATED |
| IMU | IMPLEMENTED ideal teaching IMU | Installed GAIA IMU UNKNOWN | Gazebo → ROS IMPLEMENTED | Historical smoke checks delivery/frame; physical calibration NOT VALIDATED |
| ros2_control | IMPLEMENTED Gazebo backend | Physical component PLANNED | Simulation only | Model tests + historical controller/head smoke; no physical integration proof |
| Kinematics/estimation | OP3 joint model/TF exists | GAIA model and estimator PLANNED | Global free-base estimation absent | Whole-robot estimates NOT VALIDATED |
| Balance/locomotion | Free-base option exists, no balance/gait controller here | PLANNED | No | Fixed support does not demonstrate standing or walking |
| Perception/autonomy | Camera available; algorithms PLANNED | Sensors/configuration UNKNOWN | No | No detection/navigation/task success claims |
| Digital Twin | Virtual environment only | No calibrated paired GAIA system demonstrated | PLANNED | Physical/virtual comparison NOT VALIDATED |

Evidence: [model adapter](../ros2/gaia_op3_sim/gaia_op3_sim/model.py), [launch](../ros2/gaia_op3_sim/launch/lab.launch.py), [controller configuration](../ros2/gaia_op3_sim/config/controllers.yaml), [bridge](../ros2/gaia_op3_sim/config/bridge.yaml), [actuator integration boundary](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/gaia_lx225.md), [mechanical model contract](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md).

## Existing simulation freshness checks

The [smoke test](../ros2/gaia_op3_sim/test/smoke_sim.py) observes five streams for 10 seconds of host time, requires at least two new messages per stream and final message age ≤5 seconds. It writes counts, observed rates and host ages to `smoke-metrics.json`; the workflow preserves metrics/logs even on failure. Head-motion acceptance requires a post-command joint message, avoiding acceptance of an old position.

These thresholds detect stopped/stale streams. They are not real-time performance requirements or validation of nominal sensor rates. Read the [historical validation record](simulacao/validacao.md) separately from [this documentation revision's checks](research/documentation-audit.md). Historical evidence is retained rather than represented as a new run.

## Priorities and missing measurements

| Priority | Problem | Next evidence |
| --- | --- | --- |
| CRITICAL before physical motion | Unknown joint mapping/limits and unvalidated support/stop behavior | Identified assembly, reviewed constraints and physical safety procedure |
| HIGH | GAIA mass/COM/inertia, axes and final DOF arrangement unavailable | CAD/measurement contract and one independently checked joint |
| HIGH | No fresh host-specific Gazebo throughput/visual evidence | Execute lab on identified Ubuntu/graphics hardware and retain metrics |
| HIGH | No measured physical bus timing or external position accuracy | Single-actuator read-only/controlled bench experiment |
| MEDIUM | No calibrated joint model or physical ROS backend | Identification, interface design review and fault-injection evidence |
| LOW for current teaching lab | Expanded movements/perception scenarios | Bounded experiment with a declared metric; avoid claiming walking |

Study the smoke test, simulation versus monotonic clocks, sensor QoS and coordinate frames. Challenge: interrupt the camera in an isolated simulation and verify failure. Do not run that test against physical hardware.

<!-- Legacy section fragments retained for inbound links. -->
<a id="evidencia-do-laboratorio-de-simulacao"></a>
