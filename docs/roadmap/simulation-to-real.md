# From simulation to the real robot

[Status](../engineering-status.md) · [Learning path](../learning-path.md)

Research chooses a hypothesis and its limits. Implementation makes a component executable. Validation compares observations against a predeclared criterion. Completing one does not complete the other two. Progress is gated by evidence, not a fixed calendar.

| Gate | Research question | Implementation deliverable | Validation before advancing |
| --- | --- | --- | --- |
| 0 — OP3 learning | How does a target become a joint state? | Existing isolated OP3 lab | Active controllers, fresh sensors, head out-and-back; record environment/revisions |
| 1 — ROS architecture | Which interfaces can be shared? | Document names, units, frames, clock and ownership | Trace command and feedback without confusing simulated and physical channels |
| 2 — GAIA mechanical data | What is the actual assembly? | CAD revision and per-link/per-joint data from structure repo | Dimensions, axes, limits and provenance reviewed; missing entries remain UNKNOWN |
| 3 — Kinematics | Is the chain/frame convention correct? | Symbolic/numeric chain from GAIA axes and transforms | Check poses and Jacobians against independent CAD/measurement examples |
| 4 — URDF/Xacro | Does the model represent that assembly? | Named GAIA description separate from OP3 | Unit/frame/tree/inertia checks and visual comparison; no borrowed masses |
| 5 — Gazebo model | Which effects can be represented? | GAIA collision/inertial model and virtual interfaces | Gravity/contact/simple-joint tests with assumptions and model limitations |
| 6 — One LX-225 | What does feedback actually measure? | Read-only bench acquisition then bounded motion | Identified servo/fixture/supply; external reference and stop strategy; logs retained |
| 7 — Calibration | How do servo units map to joint position? | Per-joint zero/sign/ratio/limits and version | Direction/endpoint/round-trip checks; report repeatability and uncertainty |
| 8 — Physical ROS interface | Can timing and faults be bounded? | Hardware component after contract/design review | Mock failures plus measured one-joint integration; distinguish estimated velocity |
| 9 — Multi-joint bench | What changes under shared-bus load? | Scheduling and joint-map implementation | Freshness, loss, bandwidth, power and faults measured under defined load |
| 10 — Leg/arm subsystem | Does the assembly follow bounded motion? | Supported subsystem experiment | Collision clearance, signs, limits, load and independent stop checks |
| 11 — Suspended robot | Are whole-system interfaces consistent? | Supported full assembly, conservative motion | Mapping and fault recovery checked without weight-bearing assumptions |
| 12 — Restrained standing | Can static support be evaluated safely? | Instrumented standing experiment | Load distribution, support, fall/stop procedure and measured criteria approved |
| 13 — Balance | Does feedback reject defined disturbances? | Estimator/controller with documented model | Repeat trials, tracking/fall metrics, disturbances and uncertainty recorded |
| 14 — Locomotion | Can motion start, step and stop reliably? | Footstep/trajectory/control integration | Repeated bounded trials, success/fall/stop metrics and defined operating envelope |
| 15 — Physical/virtual comparison | Where and why do observations differ? | Time-aligned recorded experiments | Independent validation data, residuals, uncertainty and explicit validity domain |

Perception can be studied in parallel with OP3 images, but physical autonomous motion depends on the earlier mechanical/control gates. No detector, gait or localization package is selected merely to complete this table.

## GAIA model input contract

Use [the mechanical model document](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md) as source of truth. For each link collect revision, geometry/scale, mass (kg), center of mass (m), inertia (kg·m²) and reference frame. For each joint collect parent/child, origin/axis, limits (rad), physical zero/sign and actuator mapping. CAD-derived values are CALCULATED with material assumptions until verified; weigh/measure the relevant assembly and retain uncertainty.

Do not replace missing inertia with an external humanoid's numbers. If a simplified research model is needed, label every assumption and restrict its use; it is not a validated GAIA model. The current final GAIA DOF count, joint arrangement and dynamic parameters remain UNKNOWN/NOT_MEASURED.

## What to study next

Understand the [hardware contract](../architecture/hardware-integration.md), then define one measured joint experiment before scaling. The [Digital Twin roadmap](digital-twin.md) explains the final comparison gate. No new repository or placeholder package is created by this roadmap.
