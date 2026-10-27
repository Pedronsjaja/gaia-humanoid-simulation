# Simulation learning index

[Home](../../README.md) · [Learning path](../learning-path.md)

The laboratory uses the official ROBOTIS OP3 model as a learning platform. GAIA’s own mechanics and LX-225 characterization belong to separate repositories. OP3 is not a measured GAIA model.

| Start here | Purpose |
| --- | --- |
| [Installation and first lab](instalacao-e-laboratorio.md) | Prepare Jazzy/Harmonic, observe sensors and command a simulated head joint |
| [System overview](../architecture/system-overview.md) | Understand every layer and its implementation status |
| [ROS 2 / simulation runtime](../architecture/ros2-architecture.md) | Locate launch, model adapter, controllers, world and bridge |
| [OP3 versus GAIA](../research/op3-reference-model.md) | Understand provenance and model replacement gates |
| [Simulation-to-real roadmap](../roadmap/simulation-to-real.md) | Progress through mechanical data, calibration and physical integration |
| [Status matrix](../engineering-status.md) | Separate implementation, measurement and validation |
| [Test strategy](../test-strategy.md) | Reproduce adapter tests and isolated Gazebo checks |
| [Historical validation](validacao.md) | Retained results, dated environments and pending manual checks |
| [Historical source audit](referencias.md) | Original model revisions and integration rationale |
| [Autonomy team lesson (pt-BR)](autonomia.md) | Earlier conceptual discussion; proposed milestones are not completed results |

Ubuntu hosts ROS tools; Gazebo computes virtual physics/sensors; URDF/Xacro describes bodies/joints; ros2_control connects controllers to joint interfaces; RViz visualizes ROS state. ROS 1/catkin or Gazebo Classic recipes are not the Jazzy/Harmonic lab.

The fixed support is a teaching device, not proof of balance. The lab does not launch `op3_manager`, open a serial port or use BusLinker. A free-base fall may be expected without a balance controller. Keep physical controllers out of the exercise domain.

Existing Portuguese historical/team lessons and their paths remain available during the English R&D migration. New canonical architecture and onboarding are linked above.

<!-- Legacy section fragments retained for inbound links. -->
<a id="simulação-do-humanoide-gaia--referência-robotis-op3"></a>
<a id="o-que-cada-programa-faz"></a>
<a id="sequência-para-a-equipe"></a>
<a id="o-que-está-entregue-agora"></a>
<a id="por-que-um-laboratório-gaia-além-do-lançador-oficial"></a>
<a id="validação"></a>
