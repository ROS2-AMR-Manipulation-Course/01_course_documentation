# Software Requirements

**Navigation:** [Course README](README.md) · [Syllabus](course_syllabus.md) · [Learning Outcomes](learning_outcomes.md) · **Software** · [Assessment](assessment_policy.md)

---

All students use the same environment, so lab instructions, package names, and error messages match for everyone.

## 1. Standard Course Environment

| Component         | Version / package                 | Used in        |
| ----------------- | --------------------------------- | -------------- |
| Operating system  | Ubuntu 24.04 LTS (64-bit)         | All weeks      |
| ROS 2             | Jazzy Jalisco (desktop install)   | All weeks      |
| Simulator         | Gazebo Harmonic (via `ros-jazzy-ros-gz`) | Weeks 2–4 |
| Visualization     | RViz2                             | Weeks 2–4      |
| Robot description | URDF, Xacro, TF2                  | Weeks 2–4      |
| Mapping           | SLAM Toolbox                      | Week 3         |
| Navigation        | Nav2                              | Week 3         |
| Manipulation      | MoveIt 2                          | Week 4         |
| Version control   | Git / GitHub                      | All weeks      |

> **Not every package is needed every week.** Install the base environment first, then the packages for each week (Section 5).

```text
Ubuntu 24.04 LTS
      │
      ▼
 ROS 2 Jazzy ──────────────┬──────────────┬───────────────┐
      │                    │              │               │
   RViz2            Gazebo Harmonic    Nav2 +        MoveIt 2
 (Weeks 2–4)        via ros_gz         SLAM Toolbox  (Week 4)
                    (Weeks 2–4)        (Week 3)
```

---

## 2. Recommended Computer

| Item     | Recommended minimum                                    |
| -------- | ------------------------------------------------------ |
| CPU      | 4 cores                                                |
| RAM      | 16 GB                                                  |
| Storage  | 250 GB available                                       |
| GPU      | Dedicated GPU recommended for Gazebo; working OpenGL drivers required for the Gazebo window |

**Virtual machines:** possible for Weeks 1–2, but Gazebo needs 3D acceleration. Without it, use the server-only fallback described in the [Week 2 lab guide](weeks/week02_kinematics_simulation/lab/lab_guide.md#116-gazebo-spawn-failures). A native Ubuntu installation is recommended for Weeks 3–4.

---

## 3. Base Installation

### 3.1 Ubuntu

Install **Ubuntu 24.04 LTS 64-bit** and verify:

```bash
lsb_release -a
```

### 3.2 ROS 2 Jazzy

Follow the official instructions: [Ubuntu (deb packages) installation — ROS 2 Jazzy](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html). Install the **desktop** variant (`ros-jazzy-desktop`), which includes RViz2 and the demo nodes.

Make every new terminal source ROS 2:

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
```

### 3.3 Course tools

```bash
sudo apt update
sudo apt install -y git python3-colcon-common-extensions
```

Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

### 3.4 Network isolation (classrooms)

ROS 2 nodes discover every other node on the same network. In a lab room, give each student a unique domain ID (instructions in [Week 1 Lab Guide, Section 2.2](weeks/week01_ros2_architecture/lab/lab_guide.md)):

```bash
echo "export ROS_DOMAIN_ID=<your_number>" >> ~/.bashrc
```

---

## 4. Verify the Environment

```bash
source /opt/ros/jazzy/setup.bash
echo $ROS_DISTRO                    # jazzy
```

Talker/listener test (two terminals):

```bash
ros2 run demo_nodes_cpp talker
ros2 run demo_nodes_py listener
```

The listener should print the talker's messages. Then check RViz2:

```bash
rviz2
```

After installing the Week 2 packages, check Gazebo Harmonic:

```bash
gz sim --versions                   # must print 8.x (Harmonic)
gz sim shapes.sdf
```

### Checklist

```text
[ ] Ubuntu 24.04 installed
[ ] Git installed and configured; GitHub account available
[ ] ROS 2 Jazzy installed; talker/listener works
[ ] RViz2 opens
[ ] ROS_DOMAIN_ID set (classroom)
[ ] Gazebo Harmonic works (before Week 2)
[ ] Workspace ~/ros2_ws builds with colcon
```

---

## 5. Packages per Week

| Week | Install                                                                                                     | Purpose |
| ---- | ----------------------------------------------------------------------------------------------------------- | ------- |
| 1    | `ros-jazzy-turtlesim ros-jazzy-joy ros-jazzy-rqt-graph ros-jazzy-rqt-console ros-jazzy-example-interfaces`    | Turtlesim, PS4 input, graph and log tools |
| 2    | `ros-jazzy-xacro ros-jazzy-robot-state-publisher ros-jazzy-joint-state-publisher-gui ros-jazzy-rviz2 ros-jazzy-tf2-tools ros-jazzy-ros-gz ros-jazzy-teleop-twist-keyboard liburdfdom-tools` | URDF/Xacro, TF2, Gazebo Harmonic + bridge |
| 3    | `ros-jazzy-navigation2 ros-jazzy-nav2-bringup ros-jazzy-slam-toolbox ros-jazzy-robot-localization` (exact list confirmed when the Week 3 lab is published) | Nav2, SLAM, state estimation |
| 4    | `ros-jazzy-moveit` (+ robot-specific packages, see [robot platforms](robot_platforms/README.md); exact list confirmed when the Week 4 lab is published) | MoveIt 2 |

Install with `sudo apt install -y <packages>`.

> **Gazebo Harmonic on Jazzy:** `ros-jazzy-ros-gz` installs Gazebo Harmonic as ROS vendor packages, matching the [official ROS–Gazebo pairing](https://gazebosim.org/docs/harmonic/ros_installation). **Do not install Gazebo Classic** (`gazebo`, `gazebo_ros_pkgs`); its instructions do not apply to this course.

---

## 6. Course Workspace

Use one workspace for the course labs:

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build --symlink-install
source install/setup.bash
```

Every terminal that runs course packages must source both ROS 2 and the workspace. External robot repositories (Trailobot, IRB 1200) can be cloned into the same `src/` folder or a separate workspace, as described in [02_robot_simulation](../../02_robot_simulation/README.md).

---

## 7. Development Tools

Git, GitHub, VS Code (or another editor), the terminal, Python 3, and C++ (optional). Useful VS Code extensions: the Python extension and an XML extension for URDF/Xacro.

---

## 8. Troubleshooting

Before reinstalling anything:

1. Check the Ubuntu version (`lsb_release -a`).
2. Source ROS 2 (`source /opt/ros/jazzy/setup.bash`) and check `echo $ROS_DISTRO`.
3. Check the package is installed (`ros2 pkg list | grep <name>`).
4. Rebuild and re-source the workspace.
5. Read the **first** error message in the terminal.
6. Use the troubleshooting section of the current week's lab guide.

Do not reinstall the whole system before identifying the actual problem.
