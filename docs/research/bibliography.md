# Technical bibliography and application notes

[Historical pinned-source audit](../simulacao/referencias.md) · [External comparison](reference-humanoid-comparison.md)

Sources checked 2026-10-26/27. These are primary technical sources for the documentation change, not a systematic literature review. No unverified paper, DOI or physical performance result is cited as evidence.

| Source | Problem / method / application | Variables and reproduction | Limit / unsupported conclusion |
| --- | --- | --- | --- |
| [Gazebo ROS installation](https://gazebosim.org/docs/harmonic/ros_installation/) | Select compatible ROS/Gazebo stack; retain Jazzy/Harmonic | Ubuntu version and package versions; follow lab and record installed environment | Compatibility documentation does not prove local graphics/performance |
| [gz_ros2_control Jazzy](https://control.ros.org/jazzy/doc/gz_ros2_control/doc/index.html) | Connect simulated joint interfaces to controller manager | Plugin declaration, command/state interfaces; inspect model adapter and run smoke | Simulated interface is not a physical LX-225 driver |
| [Joint trajectory controller](https://control.ros.org/jazzy/doc/ros2_controllers/joint_trajectory_controller/doc/userdoc.html) | Time-indexed joint targets and topic/action semantics | Joint names, rad, duration, state and feedback; head experiment | Joint trajectories do not supply balance or gait planning |
| [Hardware component guide](https://control.ros.org/jazzy/doc/ros2_control/hardware_interface/doc/writing_new_hardware_component.html) | Plugin lifecycle and read/write boundary for future physical integration | Installed Jazzy headers, lifecycle/failure tests and measured bus timing | Guide is not proof of real-time performance or safety |
| [ros_gz bridge source documentation](https://github.com/gazebosim/ros_gz/blob/ros2/ros_gz_bridge/README.md) | Typed message bridge between Gazebo and ROS | Compare supported mappings with local bridge YAML | A supported type alone does not prove a running sensor |
| [REP-103 source](https://github.com/ros-infrastructure/rep/blob/master/rep-0103.rst) | SI units and coordinate conventions | Check rad/m/s and camera/body frames | Convention does not provide physical calibration |
| [ROBOTIS OP3 simulation manual](https://emanual.robotis.com/docs/en/platform/op3/simulation/) | Vendor simulation context | Distinguish ROS generations; local pinned dependency remains authoritative | Not a GAIA mechanical or LX-225 specification |
| [Pinned OP3 source](https://github.com/ROBOTIS-GIT/ROBOTIS-OP3-Common/tree/6f7d56ccef6f78061925c56c6bd9d134d243f911) | Inspect actual model/license; reproduce adapter checks | Xacro expansion and tests at exact SHA | OP3 physical parameters must not be assigned to GAIA |
| [Actuator sources and datasheets index](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/datasheets/README.md) | Locate model-specific electrical/protocol sources before bench work | Confirm exact component and document revision | No torque/current/accuracy numbers newly asserted by this audit |

The ROS-hosted bridge/REP pages denied automated access during this session; upstream GitHub documentation was used instead. Links are access observations, not guarantees of permanent availability. Private-reference claims are limited to the inspected revision and remain NOT TESTED.
