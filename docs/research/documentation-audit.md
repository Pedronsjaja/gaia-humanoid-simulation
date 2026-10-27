# GAIA documentation and architecture audit

Audit date: 2026-10-26/27. Scope: architecture clarity, technical didactics, research organization and onboarding. This report distinguishes static inspection, automatic tests and historical simulation evidence.

## Repository audit

| Repository | Baseline | Findings |
| --- | --- | --- |
| gaia-humanoid-simulation | `a3bc0e3` | Jazzy/Harmonic Python package; pinned OP3 adapter; fixed/free support; position controller; camera/IMU bridge; five adapter tests and isolated smoke workflow |
| gaia-lx225-buslinker | `61ec128` | Python serial/bench implementation and mocked tests; position/voltage/temperature feedback; velocity derived from positions; ROS hardware backend remains planned |
| gaia-humanoid-structure | `4e64962` | CAD/support inventory and 29-entry manifest; mechanical input contract; incomplete measured GAIA model; joystick firmware diagnoses buttons/OLED, not servo actuation |
| ViniciusAbrao/humanoide | `61484165b6eb78ef5ef1d28dfbb1e2078cdcf04b` | Private comparison inspected through authorized account; OP3 trajectory/simulation/analysis workflow; not executed or copied |

Reviewed READMEs/AGENTS, relevant source/configuration/tests, dependency manifest, historical validation, mechanics/actuation boundaries and licenses. Git baselines were clean and synchronized when work started. No TODO/FIXME markers were found by the targeted search of simulation source/manifest/docs; absence of markers does not mean absence of missing work.

Remote issue/PR inspection found no open simulation items and an existing documentation PR #1 in each companion repo. Those PRs add a link to the earlier camera-to-servo lesson and remain untouched; README changes may need reconciliation when they are merged. No concurrent remote branch was overwritten.

The old tutorial ended with a validation link pointing to the references page; it now points to `validacao.md`. Existing technical boundaries were mostly correct but dispersed across Portuguese lessons. New navigation consolidates the answers without duplicating the actuator or mechanical manuals.

## Reference comparison

See [the comparison](reference-humanoid-comparison.md). The useful general lesson is to separate generation, replay, acquisition and analysis with explicit time/provenance. The main reference experiment uses Gazebo Transport rather than GAIA's ROS 2 integration. Offline stability estimates, simulated position control and physical balance validation are different claims.

No root license was found for custom reference material; the vendored ROBOTIS component has its own Apache-2.0 license. No private code, CAD, plots, trajectory data or parameters were imported. This locally prepared comparison needs disclosure review before public publication.

## Architecture decisions

The [decision record](../engineering-decisions/2026-10-27-documentation-boundaries.md) preserves three repositories and OP3 as a learning platform. The future physical interface is a requirements/contract document, not an empty or pretend implemented package. GAIA geometry, final joints and dynamics must come from its own mechanical work.

Priority: CRITICAL physical calibration/stop constraints before motion; HIGH measured GAIA model inputs and host-specific simulation/bus evidence; MEDIUM calibrated one-joint integration; LOW expansion of teaching scenarios. See the [status matrix](../engineering-status.md) for evidence and ownership.

## Documentation changes

- [System overview](../architecture/system-overview.md): all layers, source ownership and main Mermaid diagram.
- [ROS runtime](../architecture/ros2-architecture.md): code-to-interface map, clocks, sensors and control path.
- [Hardware contract](../architecture/hardware-integration.md): names/units, calibration, lifecycle, serial ownership, timing/fault requirements and design alternatives.
- [OP3 role](op3-reference-model.md), [comparison](reference-humanoid-comparison.md), [bibliography](bibliography.md) and [credits](../../THIRD_PARTY_CREDITS.md): provenance, applicability and limits.
- [Learning path](../learning-path.md): 15 levels, each with objectives, prerequisites, files, exercise, expected outcome and validation criterion.
- [Simulation-to-real](../roadmap/simulation-to-real.md), [Digital Twin](../roadmap/digital-twin.md) and [test strategy](../test-strategy.md): separate research, implementation and validation gates.
- README, documentation indices, status and first lab revised; previous entrypoint fragments retained. No existing source/test/launch/manifest/CAD asset removed or changed.

## Language migration

Primary README and AGENTS are English in all three GAIA repos. Simulation indices/status/lab and new technical documentation are English. Technical Python comments/docstrings already inspected in the simulation package are English; runtime source was intentionally left unchanged.

Existing Portuguese camera-to-servo, autonomy and historical validation/source lessons remain supplementary material with navigation notices. Their original body content, evidence links and paths are preserved. Companion repositories' deeper technical guides remain transitional Portuguese coverage, explicitly acknowledged in their READMEs. No unrelated management, marketing or internal-operation content was translated. The global user policy now records English R&D and pt-BR team/management work.

## Validation

Environment: Windows, Python 3.12.14 in an isolated audit venv, xacro 2.1.1, PyYAML 6.0.3. Upstream OP3 was checked out separately at the manifest SHA. ROS/Gazebo were not found on PATH; `wsl --list --quiet` reported WSL not installed.

| Check | Command / method | Result and limit |
| --- | --- | --- |
| OP3 adapter | `python -m unittest discover -s ros2/gaia_op3_sim/test -v`, with `PYTHONPATH` pointing to package and `OP3_DESCRIPTION_PATH` to pinned external description | **5 passed — TESTED AUTOMATICALLY**; no ROS launch or Gazebo runtime |
| LX-225 unit suite | From actuator repo: `python -m unittest discover -s tests -v` | **59 passed — TESTED AUTOMATICALLY**, mocked serial; printed telemetry is fixture data, not real measurements |
| Mechanical inventory | Recompute SHA-256 for every path in `hardware/manifest.json` | **29 matched, 0 mismatches**; integrity only, no native CAD/strength validation |
| Documentation links | Resolve Markdown local targets/fragments; public HTTP checks and authenticated GitHub API paths | 251 local links/fragments resolved, 0 errors; 65 external URLs returned HTTP 200 via public HTTP or authenticated GitHub API. External fragment rendering is not guaranteed by HTTP success |
| Diff and scope | `git diff --check`; compare changed paths and original source/model files | Documentation/instructions only; no runtime or CAD modification |
| Independent static review | Compare claims with simulation code and private-reference audit | Corrected an overclaim: smoke checks arrival freshness, not advancing `/clock`; lab now explains a manual clock check |
| Simulation / hardware | No execution in this audit | **NOT TESTED**; retained [historical Linux evidence](../simulacao/validacao.md) is not a fresh run |

The original simulation evidence remains dated and linked. The workflow filters source/config paths, so a documentation-only push would not automatically rerun simulation jobs. Follow [test strategy](../test-strategy.md) on a prepared Ubuntu host for a fresh runtime result.

## Remaining unknowns

Final GAIA joint count/arrangement, physical servo-to-joint mapping, actual installed sensors/electronics and approved mechanical limits: UNKNOWN. Measured link mass/COM/inertia, backlash, externally referenced servo accuracy, load response, serial timing and whole-system dynamics: NOT_MEASURED in this audit. CAD/third-party licensing must be resolved before reuse; model or hash validation does not resolve it.

## Next engineering steps

GAIA CAD and mechanical measurements → GAIA kinematics/URDF → Gazebo model checks → one LX-225 characterization/calibration → reviewed physical ros2_control interface → supported multi-joint/subsystem tests → calibrated model validation → physical/virtual synchronization → bounded Digital Twin experiments.

To understand this implementation, trace one simulated head command and its feedback before studying a physical backend. Study the [learning path](../learning-path.md). Challenge: describe which additional evidence would be needed to turn the same joint exercise into a defensible physical/virtual comparison.
