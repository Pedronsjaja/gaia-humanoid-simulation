# External humanoid comparison

[Overview](../architecture/system-overview.md) · [OP3 role](op3-reference-model.md)

Reference: [ViniciusAbrao/humanoide](https://github.com/ViniciusAbrao/humanoide), private repository, revision `61484165b6eb78ef5ef1d28dfbb1e2078cdcf04b`, inspected through the maintainer's authorized account on 2026-10-26/27. Public unauthenticated access returned 404; that did not mean the repository was absent. Readers need their own access.

This is an independently written architectural comparison, **not authorization to redistribute private material**. No private source, CAD, plots, trajectories or physical parameters were copied into GAIA. Publication covers this summary, not the private source materials. Inspection is STATIC / NOT TESTED: reference programs and experiments were not executed.

## What was inspected

The README and actual content of the trajectory generator, Gazebo controller, kinematics/ZMP analysis, active SDF, URDF/Xacro and package/license files were read. Key evidence paths in the reference checkout: `python/main_flecha.py`, `python/op3_multiciclos_gazebo_com_forcas.py`, `python/direto.py`, `python/inverso.py`, `python/zmp.py`, `ik_robotis/op3_ciclo_caminhada_robotis.py`, `op3_custom/`, and `ROBOTIS-OP3-Common/LICENSE`. Filenames alone were not treated as evidence of working behavior.

## Comparison

| System | Vinicius reference: verified by source inspection | GAIA | Status / limitation |
| --- | --- | --- | --- |
| Architecture | OP3 trajectory-generation, simulation replay, logging and offline analysis | ROS 2 teaching simulation plus separate physical/mechanical repos | Different emphases; no wholesale architecture replacement |
| Mechanics/CAD | OP3-based SDF, URDF/Xacro and mesh assets | Custom mechanical design owned by structure repo | Final GAIA configuration UNKNOWN; reference is not GAIA geometry |
| Actuators | Simulated per-joint position-control plugins | Hiwonder LX-225 / BusLinker bench stack | Physical reference actuators UNKNOWN; no parameter transfer |
| Degrees of freedom | Inspected OP3 model has 20 revolute joints | Final GAIA DOF count UNKNOWN | Model count is not a physical assembly measurement |
| Joint arrangement | OP3 leg/arm/head chain; trajectory focuses on legs | Must follow GAIA CAD and calibration | GAIA arrangement UNKNOWN until confirmed |
| Electronics | No project-specific physical electronics implementation located | BusLinker bench documentation; complete robot electronics UNKNOWN | Upstream vendor links do not prove installed hardware |
| Communication | Main experiment uses Python Gazebo Transport | ROS 2 interfaces in simulation; serial bench in another repo | Physical ROS bridge PLANNED |
| Control | Time-indexed reference trajectory plus local simulated position control; observations logged | ros2_control joint trajectory controller with simulated backend | Neither establishes physical balance control |
| Simulation | Gazebo-based OP3 experiment; legacy ROS assets also present | Jazzy/Harmonic lab with pinned upstream description | Reference not rerun; legacy files do not establish ROS 2 support |
| Robot model | Modified OP3 reference representation | OP3 now → separate future GAIA model | GAIA dynamic model NOT VALIDATED |
| Sensors | Model declares IMU/camera/contact sensors; experiment consumes state/pose/contact data | Virtual camera/IMU and ROS bridge implemented | Reference sensor declarations alone do not prove live publication |
| Locomotion | Gait/IK generation, trajectory replay and stored experiment outputs | Balance/locomotion research PLANNED | Stored outputs are not proof of repeatable stable gait |
| State / stability analysis | Approximate offline ZMP-style analysis from simplified kinematics/dynamics | Estimator and balance stack PLANNED | Not measured ZMP, not demonstrated closed-loop stabilization |
| Autonomy | No complete physical autonomous system established by inspected path | Research roadmap | UNKNOWN/PLANNED; no success claim |

## Architectural lessons, not copied implementation

Separate trajectory generation, execution, acquisition and analysis so each can be reproduced independently. Keep one explicit time base, record data age/validity and retain the distinction between unavailable data and measured zero. Distinguish local joint-position feedback from whole-body balance control and from offline stability estimates.

For GAIA, these lessons motivate clearer documentation and a future experiment contract, not imported gait code. A future independently implemented trajectory experiment should declare inputs, units, model revision, clock, joint error, contact validity and failure criteria. Use GAIA measurements for physical models and public/licensed sources for algorithms.

## License and research limitations

No root license was found for the reference's custom material. The vendored ROBOTIS component has Apache-2.0, and a package declares Apache licensing; that does not automatically license every custom file. Authorship is attributed to the repository owner and upstream contributors without inventing legal names or permission. No project-specific physical BOM, serial driver, test suite or CI was located in the inspected tree. Stored CSVs/plots were observed as artifacts, not independently reproduced experimental validation.

See [credits](../../THIRD_PARTY_CREDITS.md). GAIA's new diagrams and explanatory prose are independently authored; existing upstream OP3 dependency/attribution remains unchanged.
