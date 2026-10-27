# GAIA Humanoid Simulation

GAIA is a humanoid engineering project connecting custom mechanical design, LX-225 actuator characterization and robotics software. This repository is its **virtual engineering laboratory**, using **ROS 2 Jazzy, Gazebo Harmonic and the ROBOTIS OP3 reference model**.

**OP3 is not the GAIA robot.** It lets us learn controllers, sensors and ROS interfaces while GAIA mechanical data is prepared. This is not a complete Digital Twin or a walking GAIA implementation.

[Start learning](docs/learning-path.md) · [Run the lab](docs/simulacao/instalacao-e-laboratorio.md) · [System architecture](docs/architecture/system-overview.md) · [Status and evidence](docs/engineering-status.md)

## What exists today?

| Area | Current implementation | Boundary |
| --- | --- | --- |
| Simulation | Pinned OP3 model, fixed-base teaching support, 20 reference joints, position trajectory controller | OP3 geometry/dynamics; not measured GAIA properties |
| Virtual sensors | Camera, CameraInfo, IMU, simulated clock and joint states | Ideal teaching sensors; physical sensors are UNKNOWN |
| Physical actuation | Separate Python LX-225 / BusLinker bench driver | No implemented ROS 2 hardware interface in this lab |
| Mechanical design | Separate CAD/support inventory and model-data requirements | Complete GAIA joint arrangement and validated dynamic model are pending |
| Autonomy / Digital Twin | Research and integration roadmap | Balance, walking and physical/virtual synchronization are PLANNED |

The [status matrix](docs/engineering-status.md) separates implementation from validation. Historical Linux/Gazebo evidence is retained; it does not validate real actuators or the current Windows host.

## Architecture and repository ecosystem

| Source of truth | Read it for |
| --- | --- |
| [gaia-humanoid-structure](https://github.com/Pedronsjaja/gaia-humanoid-structure) | [CAD inventory](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/hardware/README.md), mechanics, frames and [GAIA model requirements](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md) |
| [gaia-lx225-buslinker](https://github.com/Pedronsjaja/gaia-lx225-buslinker) | [Python driver](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/servo_testbench.py), serial protocol, feedback, calibration and bench evidence |
| This repository | ROS 2 interfaces, OP3 simulation, virtual tests and integration contracts |
| [gaia-ros2-ubuntu24](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24) | Ubuntu 24.04 / ROS 2 environment preparation |

Read the [layer diagram](docs/architecture/system-overview.md), [ROS 2 runtime](docs/architecture/ros2-architecture.md) and [future hardware contract](docs/architecture/hardware-integration.md). No new perception/autonomy repository is needed until a concrete implementation and ownership boundary justify it.

<a id="inicio-rapido"></a>
## Quick start

Use Ubuntu 24.04 with ROS 2 Jazzy. Follow the [installation and first lab](docs/simulacao/instalacao-e-laboratorio.md) to install dependencies, import the pinned model and build. After that, in an isolated simulation terminal:

```bash
source /opt/ros/jazzy/setup.bash
source ~/gaia_ws/install/setup.bash
export ROS_DOMAIN_ID=42
export ROS_AUTOMATIC_DISCOVERY_RANGE=LOCALHOST
ros2 launch gaia_op3_sim lab.launch.py
```

Expected: OP3 held by the virtual support, active controllers and sensor messages. Check these in the lab before commanding the head. A visible window alone is not a passing test. `gui:=false` still needs rendering support; `fixed_base:=false` removes the support and does not add balance control.

## From reference simulation to the real robot

The physical actuator is **Hiwonder LX-225 via BusLinker**. A simulated position joint is not an LX-225 motor model. The future chain is ROS controller → calibrated hardware interface → bench driver → BusLinker → physical joint → feedback. Read the [interface requirements](docs/architecture/hardware-integration.md) before implementation.

The [simulation-to-real roadmap](docs/roadmap/simulation-to-real.md) starts with OP3 learning, then GAIA CAD/kinematics/URDF, one measured actuator and staged integration. [Digital Twin experiments](docs/roadmap/digital-twin.md) require physical measurements, synchronization, parameter identification and independent model validation.

<a id="guia-do-projeto"></a>
## Study, research and contribution

- Beginners: [learning path, levels 0–14](docs/learning-path.md).
- Model provenance: [OP3 versus GAIA](docs/research/op3-reference-model.md), [external humanoid comparison](docs/research/reference-humanoid-comparison.md), [bibliography](docs/research/bibliography.md).
- Validation: [test strategy](docs/test-strategy.md), [historical results](docs/simulacao/validacao.md), [documentation audit](docs/research/documentation-audit.md).
- Existing team lessons in Portuguese: [camera-to-servo guide](docs/programacao/da-camera-aos-servos.md), [GitHub introduction](docs/primeiros-passos-github.md), [autonomy discussion](docs/simulacao/autonomia.md). These remain linked during the English migration.

Before contributing, read [AGENTS.md](AGENTS.md). Open an issue with the affected subsystem, revision, reproduction, evidence and remaining uncertainty. Keep code/model changes separate from documentation changes. Test the relevant layer, retain raw data and source licenses, and never substitute OP3 or external-reference parameters for GAIA measurements. Technical R&D documentation is primarily English; Brazilian team/management material remains Portuguese.

Earlier team contributions and URLs remain available in [migration history](docs/migracao.md). The [MIT license](ros2/gaia_op3_sim/LICENSE) covers the local ROS package, not every external asset. See [third-party credits](THIRD_PARTY_CREDITS.md).

<!-- Legacy section fragments retained for inbound links. -->
<a id="-comece-aqui"></a>
<a id="como-integrar-os-servos-ao-ros-2-e-avançar-até-os-movimentos-do-robô"></a>
<a id="quer-experimentar-o-op3-no-gazebo"></a>
<a id="caminho-do-projeto"></a>
<a id="-documentação"></a>
<a id="roteiro-da-primeira-aula"></a>
<a id="o-que-existe"></a>
<a id="organização"></a>
<a id="repositórios-da-equipe-gaia"></a>
<a id="engenharia-e-validacao"></a>
