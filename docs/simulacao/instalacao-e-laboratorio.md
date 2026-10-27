<a id="instalar-o-simulador-e-colocar-o-op3-no-cenário"></a>
# Install the simulator and place OP3 in the scene

[Index](README.md) · Next: [GAIA model-data contract](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md)

Prerequisites: Ubuntu **24.04**, ROS 2 **Jazzy**, and a successful talker/listener test following the [environment guide](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24). Run commands in Ubuntu Bash. This lesson uses no physical hardware. OP3 is an external learning model, not an identified GAIA assembly.

**Evidence status:** the historical Linux/headless Gazebo validation recorded successful builds, model loading, sensors and a head trajectory. This documentation update was made on Windows, where ROS/Gazebo execution was unavailable; it does not constitute a new runtime test. Visual inspection and performance on your computer still require verification. Consult the [evidence and validation record](validacao.md).

<a id="1-instalar-gazebo-e-integração"></a>
## 1. Install Gazebo and its ROS integration

**Goal:** install the supported simulation dependencies.

**Concept:** ROS 2 Jazzy and Gazebo Harmonic are the intended pairing. The bridge exchanges ROS/Gazebo messages; `gz_ros2_control` connects simulated joints to ROS controllers.

**Commands:**

```bash
source /opt/ros/jazzy/setup.bash
sudo apt update
sudo apt install ros-jazzy-ros-gz ros-jazzy-gz-ros2-control \
  ros-jazzy-ros2-control ros-jazzy-ros2-controllers \
  ros-jazzy-xacro ros-jazzy-joint-state-publisher-gui \
  ros-jazzy-robot-state-publisher ros-jazzy-rviz2 \
  ros-jazzy-rqt-image-view ros-dev-tools python3-pytest
```

**Expected output:** package installation completes without unresolved dependencies.

**Validation:** check command exit status and package-manager errors before continuing. These commands install software; they do not prove the robot launches.

**Common problems:** unavailable packages can indicate an incomplete ROS apt setup or the wrong Ubuntu release. Do not add Gazebo Classic or mix Humble/Ubuntu 22.04 instructions into this workspace. References: [Gazebo/ROS installation](https://gazebosim.org/docs/harmonic/ros_installation/) and [gz_ros2_control](https://control.ros.org/jazzy/doc/gz_ros2_control/doc/index.html).

**What you learned:** the simulator, message bridge and controller integration have distinct roles.

**Next step:** fetch the local integration and its pinned upstream robot description.

<a id="2-baixar-o-projeto-e-o-modelo-oficial"></a>
## 2. Download the project and official model

**Goal:** build a reproducible workspace containing the integration and OP3 description.

**Concept:** `simulation/op3.repos` pins `ROBOTIS-OP3-Common`. Geometry and inertia come from `op3_description`; the GAIA package adds the world and simulation configuration. These upstream values are not GAIA measurements.

**Commands:** use a new workspace for this lesson.

```bash
mkdir -p ~/gaia_ws/src
cd ~/gaia_ws/src
git clone https://github.com/Pedronsjaja/gaia-humanoid-simulation.git
cd ~/gaia_ws
vcs import src < src/gaia-humanoid-simulation/simulation/op3.repos
```

Initialize rosdep if needed, then resolve dependencies and build:

```bash
if [ ! -f /etc/ros/rosdep/sources.list.d/20-default.list ]; then
  sudo rosdep init
fi
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro jazzy -y
colcon build --symlink-install --packages-up-to gaia_op3_sim
source install/setup.bash
ros2 pkg prefix op3_description
ros2 pkg prefix gaia_op3_sim
```

**Expected output:** the build succeeds and the last two commands return package paths under `~/gaia_ws/install`.

**Validation:** inspect the build summary and both package prefixes. `colcon` discovers packages nested under `ros2/`. LX-225 bench scripts live in another repository and are not part of this workspace.

**Common problems:** if the repository directory already exists, inspect `git status` and use a new workspace or update deliberately; do not clone over existing work. If Xacro later rejects the joint set, check the pinned revision instead of suppressing adapter checks.

**What you learned:** a pinned description makes the model input traceable, while the overlay contains the integration built from it.

**Next step:** prepare each terminal to use the same overlay and discovery settings.

<a id="3-preparar-cada-terminal-da-aula"></a>
## 3. Prepare every lesson terminal

**Goal:** keep all lesson processes in the same ROS environment.

**Concept:** sourcing selects the ROS installation and workspace overlay. The domain selects a discovery group; `LOCALHOST` restricts this exercise to the computer or VM.

**Commands:** repeat in every terminal participating in the simulation.

```bash
source /opt/ros/jazzy/setup.bash
source ~/gaia_ws/install/setup.bash
export ROS_DOMAIN_ID=42
export ROS_AUTOMATIC_DISCOVERY_RANGE=LOCALHOST
```

**Expected output:** setup commands normally complete silently, with no missing-file errors.

**Validation:** compare the environment in each terminal:

```bash
printf 'domain=%s discovery=%s\n' "$ROS_DOMAIN_ID" "$ROS_AUTOMATIC_DISCOVERY_RANGE"
ros2 pkg prefix gaia_op3_sim
```

Expect `domain=42 discovery=LOCALHOST` and the workspace package prefix.

**Common problems:** an unsourced terminal may not find packages; inconsistent domains can hide topics. Do not run other simulators in the same domain simultaneously.

**What you learned:** terminal environment affects discovery and package selection independently of the code.

**Next step:** launch one world and inspect its controllers and simulated state.

<a id="4-abrir-o-mundo-e-inserir-o-robô"></a>
## 4. Open the world and insert the robot

**Goal:** run the supported fixed-base OP3 scene.

**Concept:** the launch expands Xacro, publishes `robot_description`, spawns the model, and starts controllers and the sensor bridge. You do not need to drag an STL into the scene.

**Commands:** in terminal A:

```bash
ros2 launch gaia_op3_sim lab.launch.py
```

Wait for loading, then in prepared terminal B:

```bash
ros2 control list_controllers
ros2 topic echo /clock --once
ros2 topic echo /joint_states --once
```

For execution without a window, useful in a VM, stop the previous launch before starting:

```bash
ros2 launch gaia_op3_sim lab.launch.py gui:=false
```

**Expected output:** with the GUI, Gazebo shows a floor, a red box and a suspended OP3. `joint_state_broadcaster` and `joint_trajectory_controller` become **active**; joint states contain positions for 20 joints. `/clock` publishes virtual-world time.

**Validation:** verify both active controllers, advancing clock and joint-state names/positions. Repeat `ros2 topic echo /clock --once` after a short interval while the world is running and confirm that its time value increased; a single message only proves receipt. Record visual verification separately from headless topic checks.

**Common problems:** the controller spawner waits up to 120 seconds; inspect `gz_ros2_control` logs if it fails. Headless camera operation still requires EGL/Mesa/GPU rendering. If acceleration is inadequate, try `LIBGL_ALWAYS_SOFTWARE=1` before the launch command and accept lower performance. Missing meshes require checking `op3_description` and `GZ_SIM_RESOURCE_PATH`, not moving meshes individually.

**What you learned:** loading geometry, running controllers and observing state are separate checks.

**Next step:** send a small absolute head target with the base fixed.

<a id="5-primeiro-movimento-cabeça"></a>
## 5. First movement: the head

**Goal:** exercise the complete command-to-observation chain on one simulated joint.

**Concept:** the target is **0.15 absolute radians**, approximately 8.6 degrees, in the OP3 joint frame. It is neither a relative displacement nor an LX-225 bench angle in degrees. This lab permits partial trajectories; other joints retain their targets.

**Commands:** with active controllers and the base **fixed**:

```bash
ros2 topic pub --once /joint_trajectory_controller/joint_trajectory trajectory_msgs/msg/JointTrajectory "{joint_names: ['head_pan'], points: [{positions: [0.15], time_from_start: {sec: 2}}]}"
ros2 topic echo /joint_states --once
```

To return to zero, repeat the publication with `positions: [0.0]`.

**Expected output:** the head moves toward the requested target. In `/joint_states`, find `head_pan` in `name` and read the matching element of `position`.

**Validation:** after allowing the trajectory to execute, compare commanded and observed state; do not treat successful message publication as proof of motion. Record the observed value and timing rather than inventing a tracking tolerance.

**Common problems:** no motion can result from an inactive controller, wrong joint name, absent subscriber or incorrect units. A single immediate state sample may precede completion.

**What you learned:** message → controller → simulated joint → feedback is a testable chain. This exercise is not a walking algorithm.

**Next step:** inspect the simulated sensor streams and their time basis.

<a id="6-ver-câmera-e-imu"></a>
## 6. Inspect the camera and IMU

**Goal:** observe sensor messages and a camera image.

**Concept:** the IMU is attached to the torso; the head camera is a teaching configuration. Configured rates are 50 Hz for the IMU and 15 Hz for the camera in simulation time. Observed wall-clock rates can be lower when simulation runs slowly.

**Commands:**

```bash
ros2 topic list
ros2 topic echo /gaia/imu --once --qos-reliability best_effort
ros2 topic hz /gaia/camera/image_raw
```

Stop the rate measurement with Ctrl+C. For the image:

```bash
ros2 run rqt_image_view rqt_image_view
```

Select `/gaia/camera/image_raw`. `/gaia/camera/camera_info` is also available.

**Expected output:** an IMU sample, camera rate observations and an image in the viewer.

**Validation:** record actual topic availability and observed rate with its clock basis. Check the image visually; topic names alone do not establish useful image content. These are SIMULATED sensors, not physical GAIA sensor evidence.

**Common problems:** missing camera data requires checking Sensors/Ogre/EGL logs, the Gazebo topic, bridge and graphics acceleration. The IMU command requests best-effort reliability to match sensor delivery.

**What you learned:** configured update rate and observed delivery rate are different quantities.

**Next step:** inspect frames in RViz and save a small evidence record.

<a id="7-visualizar-em-rviz-e-gravar-evidências"></a>
## 7. Visualize in RViz and record evidence

**Goal:** inspect the published robot state and preserve a reproducible short recording.

**Concept:** Gazebo computes physics; RViz displays published state. In fixed-base mode, use `world` as **Fixed Frame**, add **RobotModel** using `/robot_description`, and add **TF**. A free base needs global-base TF/state estimation; the joint tree is not global localization.

**Commands:**

```bash
rviz2 --ros-args -p use_sim_time:=true
```

In another prepared terminal, record:

```bash
mkdir -p ~/gaia_registros
cd ~/gaia_registros
ros2 bag record /clock /joint_states /gaia/imu /gaia/camera/camera_info
```

Stop recording with Ctrl+C. Include images only when needed because they substantially increase storage.

**Expected output:** RViz displays the robot and frames; the recorder creates a bag directory and closes it on interruption.

**Validation:** inspect the created bag using its actual directory name:

```bash
ros2 bag info <created_bag_directory>
```

Replace the placeholder before running. Confirm the recorded topics and message counts. Record date, manifest revision, host hardware, observed rates and commands. Preserve raw experiment files outside source-code commits.

**Common problems:** stale TF or a misaligned model calls for checking `use_sim_time` and Fixed Frame. Do not start another `joint_state_publisher` to mask the issue. Missing recording topics may mean the simulator or bridge was not running.

**What you learned:** visualization aids inspection; saved messages and experiment metadata make the observation traceable.

**Next step:** shut down cleanly and choose a bounded next experiment.

<a id="8-encerrar-e-avançar"></a>
## 8. Shut down and advance

**Goal:** end this run and understand the boundary of the optional free-base experiment.

**Concept:** removing the support exposes contact and falling behavior. There is **no balance controller**; a fall does not by itself indicate installation failure. Idealized model gains or efforts must not be used to size GAIA hardware.

**Commands:** stop tools and launch processes with Ctrl+C. After mastering the fixed-base lesson, an optional new run is:

```bash
ros2 launch gaia_op3_sim lab.launch.py fixed_base:=false
```

**Expected output:** the initial run terminates. In the optional run, the support is removed and the robot may fall.

**Validation:** ensure the previous simulation is stopped before starting another. Record free-base behavior separately; it does not validate walking, balance, physical safety or GAIA dynamics.

**Common problems:** duplicate launches can cause conflicting controllers/topics. An unexpected fall in the fixed-base lesson requires checking `fixed_base`; free-base operation needs its own balance strategy.

**What you learned:** a functioning laboratory is a foundation for experiments, not evidence of a complete humanoid controller.

**Next step:** complete the [validation record](validacao.md), then study the [GAIA model-data contract](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md) and identify one actual joint before extending the model.

<a id="se-algo-falhar"></a>
## Troubleshooting reference

| Symptom | Check |
| --- | --- |
| Package not found | Workspace setup sourcing and build result |
| Xacro rejects joints | Upstream revision; use the manifest and do not suppress adapter errors |
| Controller manager absent | gz_ros2_control plugin logs and Jazzy packages; spawner waits up to 120 s |
| Controller inactive | `ros2 control list_hardware_interfaces` and logs; do not start another controller on the same interface |
| Missing meshes | op3_description and GZ_SIM_RESOURCE_PATH; do not move isolated mesh files |
| Clock does not advance | Paused world, stopped process or failed bridge |
| Missing camera | Sensors/Ogre/EGL logs, Gazebo topic, bridge and graphics acceleration |
| Misaligned RobotModel or stale TF | use_sim_time and Fixed Frame; do not open another joint_state_publisher |
| Trajectory has no effect | Active controller, joint name, radians and subscriber presence |
| Robot falls | fixed_base setting; free-base operation requires a balance strategy |

Graphical operation and dynamics still require verification on the team's computers. Consult the [validation record](validacao.md) before treating this virtual bench as validated for your environment or experiment.
