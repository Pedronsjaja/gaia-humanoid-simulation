# GAIA simulation engineering instructions

## Context and ownership
ROS 2 Jazzy / Gazebo Harmonic live in `ros2/gaia_op3_sim`. OP3 is an external learning model pinned in `simulation/op3.repos`, never measured GAIA geometry/dynamics. Read README, `docs/engineering-status.md` and relevant architecture before changes.

- This repo owns virtual ROS integration, simulation and validation contracts.
- `Pedronsjaja/gaia-humanoid-structure` owns CAD, mechanical frames/limits and model measurements.
- `Pedronsjaja/gaia-lx225-buslinker` owns serial/actuator code, calibration and bench evidence. A physical ROS hardware interface remains PLANNED.
- Keep subsystem details in their source repo; summarize and link here.

## Engineering rules
Primary R&D language: English for technical docs, comments, docstrings and identifiers. Preserve Portuguese team materials and historical URLs; use explicit migration links. Management/marketing/internal operations remain pt-BR.
Preserve working launch/configuration, pinned dependencies, raw data, attribution and historical validation. No copied private-reference content, CAD or parameters without compatible authorization/license. The package MIT license does not license upstream or unrelated CAD.
Use SI and identify SIMULATED, REAL, CALCULATED, ESTIMATED and assumptions. Missing design data: UNKNOWN; missing measurements: NOT_MEASURED. Never invent rates, masses, inertia, limits, torque or results. Static inspection and mocked tests are not hardware validation.

## Validation and completion
Use `.github/workflows/simulation.yml` for Linux colcon, adapter unit tests and Gazebo smoke. `python3 -m unittest discover -s ros2/gaia_op3_sim/test -v` requires xacro/YAML and the pinned OP3 description; see `docs/test-strategy.md` for the non-ROS override.
Never run `smoke_sim.py` against hardware. Never infer physical safety from simulation. Document test commands, environment, results and limits; retain evidence even on failures. Check Markdown links/anchors and `git diff --check` for docs changes.
Definition of Done: scoped changes, evidence-backed claims, reproducible validation, explicit unknowns and next gate. Follow the learning path and roadmap instead of creating placeholder packages or unnecessary repositories.
