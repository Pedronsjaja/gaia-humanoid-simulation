# Third-party sources and reuse boundaries

| Source / author | License evidence | What is used / influence |
| --- | --- | --- |
| [ROBOTIS-OP3-Common / ROBOTIS and contributors](https://github.com/ROBOTIS-GIT/ROBOTIS-OP3-Common/tree/6f7d56ccef6f78061925c56c6bd9d134d243f911) | [Apache-2.0 at pinned revision](https://github.com/ROBOTIS-GIT/ROBOTIS-OP3-Common/blob/6f7d56ccef6f78061925c56c6bd9d134d243f911/LICENSE) | Existing externally downloaded reference model; adapter preserves geometry/dynamics and upstream files. No new model vendoring in this change. |
| [ViniciusAbrao/humanoide / repository owner and contributors](https://github.com/ViniciusAbrao/humanoide) | Private; no root license found for custom material | Authorized static comparison only. No copied code, CAD, documentation, images, diagrams, trajectories or parameters. General lesson: separate generation, execution and experiment analysis. Repository access does not grant a license to redistribute its private assets. |
| [ROS 2 / Gazebo / ros2_control documentation](docs/research/bibliography.md) | Refer to each upstream source's terms | Technical reference for independently written architecture and onboarding; links retained. |
| Existing GAIA team materials | [Migration provenance](docs/migracao.md) | Existing contributions and historical evidence preserved. |

The local [ROS package MIT license](ros2/gaia_op3_sim/LICENSE) is scoped to that package. It does not assign a new license to third-party CAD or all repository content. The structure/actuator repos currently lack a root license; missing license data remains UNKNOWN, not permission to redistribute.
