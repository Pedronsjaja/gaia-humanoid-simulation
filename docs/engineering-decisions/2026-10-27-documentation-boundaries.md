# ADR: preserve subsystem ownership and teach through evidence

Status: accepted for this documentation revision; physical interface implementation remains undecided.

Problem: readers could confuse OP3 simulation, the GAIA physical design and an external humanoid reference, or mistake existing Python bench functions for integrated ROS hardware. Portuguese technical entrypoints also conflicted with the requested primary R&D language.

Constraints: preserve working Jazzy/Harmonic launch/model/tests, external licenses, old URLs and historical results. GAIA mechanical measurements and physical interface evidence are incomplete. The external reference is private with no root license for custom content.

Alternatives: merge repositories; copy the external gait stack; replace OP3 prematurely; rename every documentation path; or preserve boundaries and add an evidence-backed navigation layer. Criteria: correctness, traceability, beginner comprehension, low regression risk and reversible changes.

Decision: retain three source-of-truth repos, OP3 teaching model and original paths. Add concise English README/architecture/status/learning/roadmap/validation documents; translate the first lab in place with legacy anchors. Keep historical Portuguese lessons available as transitional team resources. Document a future hardware contract without creating a fake package. Use independent prose/diagrams and licensed public references; publish only the independently written comparison; do not redistribute private assets.

Consequences: beginner navigation improves without runtime changes. Some historical material remains Portuguese and explicitly linked. A future hardware design must resolve transport ownership, lifecycle, timing, calibration and fault policy before implementation. A complete physical model cannot be inferred from a diagram or asset catalog.

Evidence: [repository audit](../research/documentation-audit.md), [comparison](../research/reference-humanoid-comparison.md), [bibliography](../research/bibliography.md), [hardware contract](../architecture/hardware-integration.md).
