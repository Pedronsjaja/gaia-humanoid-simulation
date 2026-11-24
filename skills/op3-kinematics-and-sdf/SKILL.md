---
name: op3-kinematics-and-sdf
description: Evidence-first inspection and safe validation of ROBOTIS OP3 URDF, Xacro, SDF, kinematic frames, and Gazebo model structure. Use when reviewing OP3 joint/link geometry, transforms, frame conventions, model conversion, or simulation discrepancies in ROS 2 Jazzy with Gazebo Harmonic / Gazebo Sim 8; preserve original geometry unless a specifically justified proposal is explicitly authorized.
---

# OP3-KINEMATICS-AND-SDF

## Mission and non-negotiable geometry invariant

Inspect, explain, compare, and validate OP3 model descriptions without silently changing their kinematics. Preserve the original ROBOTIS OP3 geometry exactly by default. Do not alter original joint origins or positions, axes or axis orientations, offsets, distances, parent-child sequence, hip/knee/ankle references, assembly, or any other transforms to compensate for gait, balance, contact, collision, or simulation behavior.

Treat a geometry change as exceptional: technically justify it with evidence, present the exact proposed diff and expected consequences before execution, and wait for the user's explicit authorization of that proposal. Never infer permission from a request to diagnose, improve, fix, or make the simulation work. If no exact geometry change has been authorized, make no geometry change.

Recognize the OP3 leg configuration as 12 leg joints total, six per side. Discover actual joint and link names in the checked-out source; do not invent or assume names, model identifiers, transforms, or local paths.

## Activate for

- Inspecting or debugging OP3 URDF, Xacro, SDF, joint graphs, frame transforms, or model conversion.
- Comparing a reference model with a candidate and determining whether kinematic geometry changed.
- Investigating Gazebo Sim loading, contact, collision, balance, or gait issues where model geometry is implicated.
- Reviewing changes to the pinned external OP3 source or its simulation integration.

Coordinate with locomotion specialists for gait, balance, and controller behavior, and Gazebo specialists for simulator, physics, contact, and SDF behavior. Keep kinematic source-of-truth analysis distinct from their subsystem diagnoses.

## Required expertise and working discipline

Apply robotics transform conventions, URDF/Xacro/XML and SDF inspection, ROS 2 tooling, and Gazebo Sim version awareness. Work evidence-first: inspect the repository, source files, tool versions, and command help before drawing conclusions. Clearly label observations, assumptions, and unknowns. Do not present structural validation as proof of physical validity.

The project context identifies ROS 2 Jazzy and Gazebo Harmonic / Gazebo Sim 8, with a local Gazebo Sim 8.11.0 claim, and pins an external OP3 under `simulation/op3.repos`. Verify all of these locally. The project explicitly distinguishes that external OP3 from measured GAIA geometry/dynamics: never conflate them or treat one as a validated proxy for the other.

## Evidence-first inspection procedure

1. **Establish provenance.** Inspect repository root, status, relevant commit/revision, submodule or vcs import state, `simulation/op3.repos`, and the exact OP3 source checkout. Record canonical paths, source repository/revision, file hashes where useful, and whether each file is tracked, generated, or locally modified. Preserve original/reference files; do not overwrite generated or source models during inspection.
2. **Verify environment.** Record available ROS distribution, `xacro`, `check_urdf`, `gz`, and Gazebo Sim versions using installed-tool version commands or package metadata. Treat expected version strings as claims, not facts. Discover supported syntax before use: run `gz sdf --help` (and relevant subcommand help) before choosing options. Do not assume every Gazebo release supports the same flags or behavior.
3. **Identify the actual model inputs.** Trace launch/configuration and model references to the source Xacro/URDF/SDF actually consumed by the simulation. Resolve Xacro includes, macros, arguments, mappings, and environment-dependent paths. Record the expansion command and arguments. Generate expanded output only to a separate temporary or review path; do not replace source files.
4. **Build the graph.** Parse the expanded XML and inspect all links and joints. For each joint, capture name, type, parent link, child link, origin translation and rotation, axis vector, and any relevant mimic/limit metadata. Trace roots, branches, and end-effectors. Check for missing links, duplicate names, disconnected components, cycles, and unexpected parent-child changes. Identify the twelve leg joints (six per side) from the source; do not rely on naming guesses.
5. **Identify and label frames.** Name the source frame, destination frame, parent link, child link, joint frame, and any simulator-specific frame explicitly. Draw or tabulate transform direction (for example, `parent_T_joint` versus `joint_T_parent`). State the coordinate handedness, axis convention, and unit convention from the source and applicable specification. URDF origins are conventionally in metres and radians; verify any alternate input or conversion rather than silently assuming. Distinguish metres from millimetres and radians from degrees. Never compare Euler components unless rotation convention and order match.
6. **Trace references through conversion.** Compare source descriptions with expanded URDF and converted/loaded SDF, distinguishing source-authored data from generated defaults or conversion artifacts. Determine which model file Gazebo actually loads. For SDF frame and axis semantics, inspect the relevant SDF version and documentation or tool output; do not transfer URDF axis-frame assumptions blindly into SDF.

## Numerical geometry comparison

Before evaluating any candidate change, establish a baseline from the authoritative, unmodified source and compare it with the candidate using a deterministic XML-aware parser or equivalent structured inspection—not visual inspection or a text diff alone. Preserve both inputs and record parser/version and unit assumptions.

Compare and report at minimum:

- Complete joint-name and link-name sets, including additions/removals/renames.
- Every joint's type, parent-child pair and graph/topological order; flag any altered parent-child sequence.
- Every joint origin's translation vector and full rotation, along with fixed-link and other relevant frame transforms.
- Every axis vector, its declared reference frame, norm, and orientation; do not compare direction numbers without frame context.
- Offsets, distances, hip/knee/ankle references, assembly, and any other transform-bearing values or conversions.
- Relevant source-to-generated changes, with explicit separation between geometry and non-geometric fields.

Normalize units before numerical comparison, but also report original units and conversions. Compare translations componentwise and by norm with stated tolerances; compare rotations using rotation matrices/quaternions or angular distance with a stated tolerance and convention. Normalize axes only for diagnostic comparison—report the original vector and norm as well. Explain floating-point/serialization differences separately from meaningful deviations. Do not select tolerances to hide a discrepancy. A round-trip that changes source numeric representations can still change geometry; resolve and report it.

A concise per-joint table should include: joint, parent, child, origin xyz, origin rotation (format/convention), axis (frame and vector), and baseline-to-candidate deltas. Summarize unchanged/changed/missing/extra elements and retain the machine-readable comparison output when available.

## Safe validation workflow

1. **Inspect source without editing.** Use repository search and read-only XML parsing. For Xacro, check `xacro --help`, expand with the actual required mappings into a separate file, and retain the exact command. Never assume one Xacro invocation is valid for all package layouts.
2. **Parse and check structure.** Use a real XML parser (for example Python's standard `xml.etree.ElementTree`) to extract the joint/link graph and numeric values. Run `check_urdf` only if installed and applicable to the expanded URDF; record its output. It checks aspects of URDF structure, not physical validity.
3. **Validate SDF with installed tooling.** First run `gz sdf --help` and confirm installed Gazebo Sim version. Use `gz sdf -k` to check an SDF only if that installed CLI documents/supports it. Use `gz sdf -p` to print/convert only if the installed CLI documents/supports that option, writing output separately. Otherwise use the version-supported documented equivalent or report the limitation. Do not claim an option exists based on another Gazebo version.
4. **Compare before/after.** Keep immutable baseline and candidate copies; produce structured numerical comparison plus an ordinary diff for review. Verify that transformations and topology match when no geometric change was authorized. Check generated outputs and simulator-loaded sources, not only the authored file.
5. **Run scoped tests.** Run applicable package tests, model checks, and launch/load smoke tests in a safe environment when available. Capture exact commands, versions, exit status, logs, and artifacts. Separate failures attributable to parser/schema/version mismatch from model defects.
6. **Interpret limits.** XML parsing, `check_urdf`, `gz sdf`, graph checks, diffs, and smoke tests can establish syntax or structural consistency only. They do not establish measured accuracy, inertial correctness, actuator behavior, contact fidelity, controller stability, or physical validity. Those require appropriate source evidence, measurements, and domain testing.

## Diagnose without altering kinematic geometry

When behavior is wrong, first separate the symptom from the model's kinematics and establish a reproducible baseline. Prefer investigating controller configuration, transmissions/actuator interfaces, joint limits, command units, update rates, physics parameters, contact/friction settings, collision geometry, sensor configuration, plugin wiring, and simulator-version compatibility as separate concerns. Do not modify joint transforms or axes to mask any of these issues. Collision and visual geometry may be separate from kinematic joint geometry, but edits to them still require backup, an exact proposed diff, rationale, and explicit user confirmation.

Do not represent the external OP3 as measured GAIA geometry/dynamics. If GAIA matching is requested, ask for or locate the authoritative measured GAIA data and provenance, then report what is and is not comparable. Do not derive a GAIA transform from gait symptoms or tune OP3 geometry to approximate an undocumented target.

## Change-control gate: applies to every edit

Before any file edit—including non-geometric configuration, collision, controller, or test edits:

1. Preserve the original using a verified copy/backup or, in a Git repository, a suitable commit/patch or Git bundle where appropriate. Confirm the backup can be identified and is not the only working copy.
2. Show the exact minimal proposed diff, target path, rationale grounded in collected evidence, expected effect, risks, and rollback method. For a geometry proposal, enumerate every affected joint/link/frame and all numeric before/after values; explain why non-geometric alternatives are insufficient.
3. Wait for explicit user confirmation of that exact proposal. Do not proceed on silence, implied consent, or a broader request to fix behavior. If the user does not authorize it, leave files unchanged and provide diagnosis or a revised proposal.
4. After approval, make only the authorized minimal, reversible change. Re-run the numerical comparison, applicable structural checks and focused tests; inspect the final diff and confirm no unintended source, transform, topology, or generated-file changes.

For any proposed geometry change, default to **no change**. Do not execute it until the user has seen and explicitly approved the exact proposal. If an edit cannot be safely isolated, backed up, or validated, stop and explain the blocker.
