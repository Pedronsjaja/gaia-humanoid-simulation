# GAIA learning path

[Home](../README.md) · [First lab](simulacao/instalacao-e-laboratorio.md) · [Engineering status](engineering-status.md)

Start with one observable result. Each level has a prerequisite and a validation gate. Levels 8–14 include research exercises and planned capabilities; they do not imply those components already exist. Perception can proceed alongside control studies once the virtual camera is understood.

Keep a study record: question, revision, commands/procedure, observations, failures, explanation and next question. Use [the validation vocabulary](test-strategy.md). Physical experiments require the identified setup and reviewed safeguards; offline exercises remain useful while hardware is unavailable.

## Level 0 — Git / GitHub

- **Objective:** Read history and propose a reproducible change.
- **Prerequisites:** A GitHub account and a local Git installation.
- **Read:** [GitHub introduction (pt-BR)](primeiros-passos-github.md), [README](../README.md) and [AGENTS](../AGENTS.md).
- **Practical exercise:** On a study branch, inspect git status/log/diff and improve one explanation without changing runtime code.
- **Expected result:** A small, reviewable documentation diff.
- **Validation criterion:** Explain the base revision, changed files and how to revert your own commit without discarding another person’s work.

## Level 1 — Linux

- **Objective:** Understand terminals, paths, permissions and environment sourcing.
- **Prerequisites:** Level 0.
- **Read:** [Ubuntu/ROS environment repo](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24) and [lab stages 1–3](simulacao/instalacao-e-laboratorio.md).
- **Practical exercise:** Record OS/version and execute the environment repo’s talker/listener exercise in separate sourced terminals.
- **Expected result:** A reproducible environment record and exchanged messages.
- **Validation criterion:** A new terminal reproduces the result; distinguish an installed package from a sourced workspace.

## Level 2 — Python

- **Objective:** Read a small pure function and its regression tests.
- **Prerequisites:** Level 1 and basic variables/functions.
- **Read:** [model.py](../ros2/gaia_op3_sim/gaia_op3_sim/model.py) and [test_model.py](../ros2/gaia_op3_sim/test/test_model.py).
- **Practical exercise:** Trace adapt_model input/output, then run the five adapter tests with the pinned model using the test strategy.
- **Expected result:** Passing tests and an explanation of the rejected upstream-joint change.
- **Validation criterion:** Explain why preserving XML geometry is tested separately from Gazebo runtime behavior.

## Level 3 — ROS 2 fundamentals

- **Objective:** Trace a typed command and observation through nodes.
- **Prerequisites:** Levels 1–2; working Jazzy environment.
- **Read:** [Runtime architecture](architecture/ros2-architecture.md) and [launch](../ros2/gaia_op3_sim/launch/lab.launch.py).
- **Practical exercise:** In the isolated simulation, inspect ros2 node list, ros2 topic list -t and ros2 control list_controllers.
- **Expected result:** A graph mapping launch components to runtime interfaces.
- **Validation criterion:** Identify publisher, subscriber, message type and clock for head commands and joint feedback; distinguish topics, services and actions.

## Level 4 — URDF / Xacro

- **Objective:** Understand links, joint axes, frames and inertia provenance.
- **Prerequisites:** Level 3; coordinates and SI units.
- **Read:** [OP3 model role](research/op3-reference-model.md), [manifest](../simulation/op3.repos) and [GAIA model contract](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md).
- **Practical exercise:** Expand the pinned Xacro through the adapter test and trace one head/leg joint to its parent, child, axis and limits.
- **Expected result:** An annotated chain with OP3 provenance.
- **Validation criterion:** Do not assign that chain or its values to GAIA; list the GAIA measurements still required.

## Level 5 — Gazebo

- **Objective:** Distinguish simulated physics from ROS visualization.
- **Prerequisites:** Levels 3–4.
- **Read:** [Lab](simulacao/instalacao-e-laboratorio.md), [world](../ros2/gaia_op3_sim/worlds/lab.sdf).
- **Practical exercise:** Run the fixed-base lab, observe the target and compare Gazebo with RViz; record simulated versus host time.
- **Expected result:** Visible model and fresh simulated observations.
- **Validation criterion:** Explain why the support prevents a balance test and why headless mode still needs rendering.

## Level 6 — ros2_control

- **Objective:** Understand command/state interfaces and trajectory execution.
- **Prerequisites:** Level 5; radians and time.
- **Read:** [controllers.yaml](../ros2/gaia_op3_sim/config/controllers.yaml), [runtime](architecture/ros2-architecture.md).
- **Practical exercise:** Execute the lab head target and return-to-zero; match joint name indexes in feedback.
- **Expected result:** A bounded target/feedback trace in simulation.
- **Validation criterion:** Use the existing smoke acceptance criteria, record errors, and explain why configured 100 Hz is not a measured bus rate.

## Level 7 — LX-225 / BusLinker

- **Objective:** Separate protocol tests from measured actuator behavior.
- **Prerequisites:** Level 2; lab/electrical safety review before any physical work.
- **Read:** [Driver](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/servo_testbench.py), [validation](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/validacao-calibracao.md), [hardware contract](architecture/hardware-integration.md).
- **Practical exercise:** Run the mocked actuator suite first. Draft a read-only one-servo experiment with identified supply/interface/fixture; physical execution requires confirmed setup.
- **Expected result:** Protocol-test evidence and a bench plan, not an automatic motion command.
- **Validation criterion:** Label internal feedback versus external reference and measured position versus estimated velocity; leave missing accuracy NOT_MEASURED.

## Level 8 — Humanoid kinematics

- **Objective:** Relate joint angles to link poses without importing another robot’s geometry.
- **Prerequisites:** Level 4; homogeneous transforms and basic linear algebra.
- **Read:** [GAIA input contract](roadmap/simulation-to-real.md) and [mechanical source](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md).
- **Practical exercise:** Build a two-link paper/notebook example with explicitly ASSUMED lengths; derive forward kinematics and check simple poses.
- **Expected result:** A labeled educational calculation, separate from GAIA data.
- **Validation criterion:** Compare zero/right-angle cases analytically; explain which measured GAIA axes/lengths would replace assumptions.

## Level 9 — State estimation

- **Objective:** Understand why observations require frames, time and uncertainty.
- **Prerequisites:** Levels 3, 5 and 8; probability basics.
- **Read:** [Runtime sensor map](architecture/ros2-architecture.md) and [autonomy discussion (pt-BR)](simulacao/autonomia.md).
- **Practical exercise:** Record simulated IMU/joint data; compare clocks and frames and specify inputs/outputs of a future estimator.
- **Expected result:** A time/frame/quality inventory and estimator hypothesis.
- **Validation criterion:** Explain why joint TF is not global free-base localization; do not claim an estimator was implemented by recording a bag.

## Level 10 — Balance

- **Objective:** Separate support geometry, state estimation and feedback control.
- **Prerequisites:** Levels 8–9; dynamics and feedback fundamentals.
- **Read:** [Roadmap gates 12–13](roadmap/simulation-to-real.md), [reference limitations](research/reference-humanoid-comparison.md).
- **Practical exercise:** On an explicitly simplified model, state the assumptions behind a support/ZMP analysis and design a bounded simulated disturbance test.
- **Expected result:** Research plan with measurable response/failure criteria.
- **Validation criterion:** Explain why offline ZMP estimates and fixed-base success do not prove balance; parameters remain assumptions until identified.

## Level 11 — Locomotion

- **Objective:** Define stepping as a reproducible experiment rather than a video claim.
- **Prerequisites:** Level 10 and a separately validated balance/control model.
- **Read:** [Simulation-to-real roadmap](roadmap/simulation-to-real.md), [test strategy](test-strategy.md).
- **Practical exercise:** Specify start/step/stop trajectories, support/contact observations, repeated trials and failure metrics before implementing an independent generator.
- **Expected result:** A simulation experiment specification; no physical gait is delivered by this lesson.
- **Validation criterion:** Require joint limits, contact validity, displacement and falls to be reported; absence of data is not zero failures.

## Level 12 — Perception

- **Objective:** Connect images to task observations with measurable error/latency.
- **Prerequisites:** Levels 2–3 and 5; image coordinates.
- **Read:** [Camera interface](architecture/ros2-architecture.md), [camera-to-servo team lesson (pt-BR)](programacao/da-camera-aos-servos.md).
- **Practical exercise:** Collect a small labeled virtual-target dataset and define an offline baseline/evaluation split before choosing a detector.
- **Expected result:** A dataset manifest and evaluation plan tied to frame/time.
- **Validation criterion:** Report false positives/negatives and latency once implemented; a received image is not a tested detection algorithm.

## Level 13 — Physical / virtual integration

- **Objective:** Preserve joint semantics while changing the backend.
- **Prerequisites:** Levels 6–9; completed single-joint bench/calibration gates.
- **Read:** [Hardware contract](architecture/hardware-integration.md), [actuator roadmap](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/digital-twin-roadmap.md).
- **Practical exercise:** Draft joint mapping and fault cases; use mocks to exercise missing calibration, stale replies and duplicate serial ownership before hardware integration.
- **Expected result:** An interface contract and test evidence with provenance.
- **Validation criterion:** Reject invalid/stale states rather than reporting fresh zeros; demonstrate why serial timing must be measured before selecting update rate.

## Level 14 — Digital Twin

- **Objective:** Design a defensible physical/virtual comparison.
- **Prerequisites:** Level 13; real measurements and a calibrated GAIA model.
- **Read:** [Digital Twin roadmap](roadmap/digital-twin.md), [test strategy](test-strategy.md).
- **Practical exercise:** Separate identification and held-out validation trajectories; specify time alignment and uncertainty, then compare only available measured signals.
- **Expected result:** A reproducible comparison report within a stated operating domain.
- **Validation criterion:** Report residuals and uncertainty on unseen data; if physical data is unavailable, call the result a plan/simulation, not a validated twin.
