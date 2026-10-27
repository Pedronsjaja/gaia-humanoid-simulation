# Robotics software learning index

[Home](../../README.md) · [Learning path](../learning-path.md)

Start with [the complete system](../architecture/system-overview.md), then trace [the ROS 2 runtime](../architecture/ros2-architecture.md). The simulation package and the physical actuator driver have separate responsibilities. A Python bench function does not become a ROS hardware component merely by appearing in a diagram.

| Material | What to learn |
| --- | --- |
| [Ubuntu / ROS environment](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24) | Prepare and verify the host |
| [First simulation lab](../simulacao/instalacao-e-laboratorio.md) | Commands, joint feedback and virtual sensors |
| [Hardware integration contract](../architecture/hardware-integration.md) | Future mapping, conversion, time, state and fault semantics |
| [Existing camera-to-servo lesson (pt-BR)](da-camera-aos-servos.md) | Illustrated team explanation; preserves historical anchors |
| [Lesson references (pt-BR)](referencias-camera-servos.md) | Earlier source inspection and limitations |
| [GitHub introduction (pt-BR)](../primeiros-passos-github.md) | Team onboarding |
| [Autonomy discussion (pt-BR)](../simulacao/autonomia.md) | Research concepts, not completed algorithms |

## Source files and subsystem owners

- [ROS package](../../ros2/gaia_op3_sim) and [OP3 manifest](../../simulation/op3.repos): virtual lab.
- [Shared LX-225 driver](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/servo_testbench.py), [bench tools](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/bench_tools.py), [V2.5](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/buslinker_v2_5.py), [V3](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/buslinker_v3.py): actuation owner; [usage](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/uso.md) and [installation](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/instalacao.md).
- [GAIA model contract](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md) and [competition requirements](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/competicao/README.md): structure/requirements owner.

Existing Portuguese lessons remain supplementary team resources during migration. Use the English architecture and status documents for the current implementation boundary; do not interpret historical proposals as working integrations.

<!-- Legacy section fragments retained for inbound links. -->
<a id="frente-de-programação"></a>
<a id="comece-pela-explicação-da-comunicação"></a>
<a id="código-local-e-integrações-em-outros-repositórios"></a>
<a id="como-trabalhar-com-as-outras-frentes"></a>
