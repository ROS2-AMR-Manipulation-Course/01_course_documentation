# Trailobot AMR — Simulation Guide

**Navigation:** [Robot Simulation](../README.md) · **Trailobot** · [ABB IRB 1200](../abb_irb1200/README.md) · [RB3-1200](../rb3_1200/README.md) · [Trailobot platform page](../../01_course_documentation/course/robot_platforms/trailobot_amr.md)

<p align="center">
  <img src="../../assets/images/robots/trailobot_isometric.jpg" alt="Trailobot AMR, three-quarter view" height="200">
  <br><sub>Trailobot AMR</sub>
</p>

> **Implementation repository:** [github.com/boyloy21/Trailobot_Simulation](https://github.com/boyloy21/Trailobot_Simulation) (branch `main`, inspected at commit `54dcda2`)
> **Repository target:** ROS 2 Humble + Gazebo Fortress · **Course target:** ROS 2 Jazzy + Gazebo Harmonic

The Trailobot code is **not copied** into this course repository. Clone the original repository and use this page as a guide. Read the [platform page](../../01_course_documentation/course/robot_platforms/trailobot_amr.md) first: it lists what is confirmed, what was tested, and the known issues.

---

## 1. What You Can Do Today (course environment)

| Activity                                                     | Status in Jazzy + Harmonic |
| ------------------------------------------------------------ | -------------------------- |
| Read and inspect the Xacro model and its frames (Week 2)     | ✅ Works (tested: `xacro` + `check_urdf`) |
| Visualize the model in RViz2                                 | Expected to work via `visualization.launch.py` — not yet tested |
| Spawn and drive in Gazebo Harmonic with the repository worlds | ⚠️ Not working as-is (see platform page, Section 6) |
| SLAM and Nav2 (Week 3)                                       | 📝 Requires the Harmonic port listed in the platform page, Section 7 |

## 2. Get the Source

```bash
mkdir -p ~/trailobot_ws/src
cd ~/trailobot_ws/src
git clone https://github.com/boyloy21/Trailobot_Simulation.git
```

## 3. Inspect the Robot Description (Week 2)

The model is `trailobot_description/urdf/robot.xacro`. It includes its parts with `$(find trailobot_description)`, so the package must be built and sourced before `xacro` can process it:

```bash
cd ~/trailobot_ws
rosdep install --from-paths src --ignore-src -r -y
colcon build --symlink-install --packages-select trailobot_description
source install/setup.bash
```

> **Not yet tested:** the course maintainers validated the Xacro files directly but have not built this package with `colcon` on Jazzy. `trailobot_description/package.xml` declares many dependencies (Nav2, ros2_control, Fortress-era `ros_ign_*` and `ign_ros2_control` packages), and some of these may not be available on Jazzy. If `rosdep` cannot resolve them, report the problem to the repository maintainer.

Then validate and explore the model:

```bash
cd /tmp
xacro $(ros2 pkg prefix --share trailobot_description)/urdf/robot.xacro GAZEBO:=true > trailobot.urdf
check_urdf trailobot.urdf
urdf_to_graphviz trailobot.urdf trailobot          # writes trailobot.pdf
gz sdf -p trailobot.urdf 2>&1 | grep -iE "warn|error"
```

Expected (tested with the source files): `check_urdf` reports root `base_footprint` with 21 child links under `base_link`, including `left_wheel_link`, `right_wheel_link`, `LidarFR_link`, `LidarBL_link`, `CameraFR_link`, `CameraFL_link`, `imu_link`, four casters, and eight ultrasonic links. `gz sdf` prints warnings for Fortress-only sensor tags.

Visualize with the repository's launch file (from the file contents: `robot_state_publisher`, joint sliders, RViz2):

```bash
ros2 launch trailobot_description visualization.launch.py
```

### Week 2 inspection questions

Use the model to answer:

1. Which joints are `continuous` and which are `fixed`? Why are the four casters fixed here, and what does that mean for the simulation?
2. What is the height of `base_link` above `base_footprint`? Calculate it from `const.xacro` (`base_height/2 + wheel_radius`).
3. What does the Xacro argument `GAZEBO` switch between?
4. Compare the `DiffDrive` `wheel_radius` and `wheel_separation` with the geometry in `const.xacro`.
5. Which sensor frames would SLAM need in Week 3?

## 4. Simulation (Gazebo)

The repository's simulation entry point is `trailobot_description/launch/ign.launch.py`. It starts Gazebo through `ros_gz_sim`, spawns the robot, and starts an EKF and a laser-scan merger. In the course environment it cannot run unchanged:

* It includes the package `ros2_laser_scan_merger`, which is not part of the repository.
* `worlds/my_world.sdf` loads models from absolute paths on the author's computer.
* The robot model loads world-level Gazebo systems, which conflicts with worlds that load them too.

A Gazebo Harmonic port is planned before the Week 3 lab is published (see the [platform page, Section 7](../../01_course_documentation/course/robot_platforms/trailobot_amr.md#7-proposed-work-before-week-3-)). **Do not attempt the Week 3 navigation workflow on Trailobot until the port is published.**

## 5. Repository Layout (confirmed)

```text
Trailobot_Simulation/
├── trailobot_description/   model (urdf/, meshes/), worlds/, models/, map/, config/, launch/, rviz/
├── trailobot_launch/        mapping, navigation, status_sensor, web_server launch files; Nav2 parameters
├── trailobot_interfaces/    srv/CancelNavigation.srv
├── trailobot_teleop/        ps4_node
└── trailobot_web/           sensor_status, imu_diagnostics, lidar_diagnostics, cancel_nav
```

## 6. Reporting Problems

Problems with the Trailobot code belong in the [Trailobot repository](https://github.com/boyloy21/Trailobot_Simulation). Problems with these instructions belong in this course repository (see [CONTRIBUTING.md](../../CONTRIBUTING.md)).
