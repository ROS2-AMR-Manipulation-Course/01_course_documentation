# Week 2 Lab Guide

## Build, Validate and Simulate a Differential-Drive Robot

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Estimated duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic
> **Main objective:** Build a mobile robot model step by step (URDF → Xacro → inertia → sensor), validate it, inspect its TF tree, and drive it in Gazebo Harmonic.

**Navigation:** [Lab README](README.md) · **Lab Guide** · [Exercises](exercises.md) · [Lecture Notes](../lecture/notes.md) · [Kinematics Examples](../lecture/kinematics_examples.md)

---

## 1. Laboratory Overview

You will build **`diffbot`**, a small differential-drive robot with the same structure as the course robot Trailobot: two driven wheels, a caster, a chassis, and a 2D LiDAR.

You will build the model in stages and check each stage before adding more:

| Stage | You add                                        | You check with                              |
| ----- | ---------------------------------------------- | ------------------------------------------- |
| 1     | Plain URDF: links, joints, visuals             | `check_urdf`, `urdf_to_graphviz`, RViz2     |
| 2     | Xacro: properties, a wheel macro, collisions   | `xacro`, `check_urdf`, RViz2                |
| 3     | Inertial properties                            | Generated `<inertia>` values                |
| 4     | LiDAR link + TF inspection                     | `view_frames`, `tf2_echo`                   |
| 5     | Gazebo plugins, world, bridge, launch          | Gazebo, `ros2 topic`, driving the robot     |

### 1.1 Final system

```text
                               ┌────────────────────── Gazebo Harmonic ──────────────────────┐
 xacro ─▶ robot_state_publisher│  diffbot model                                              │
            │ /robot_description ─▶ ros_gz_sim create (spawn)                               │
            │                  │  DiffDrive system ◀── cmd_vel     odom, tf ──┐              │
            │ /tf, /tf_static  │  JointStatePublisher system ── joint_states ─┤              │
            ▼                  │  gpu_lidar sensor ── scan                    │  clock       │
          RViz2 ◀──────────────┴──────────────────── ros_gz_bridge ◀──────────┴──────────────┘
            ▲                                            │  ▲
            └── /scan /odom /tf /joint_states /clock ◀───┘  └── /cmd_vel ◀── teleop_twist_keyboard
```

### 1.2 Final package

```text
~/ros2_ws/src/diffbot_description/
├── CMakeLists.txt
├── package.xml
├── config/
│   └── gz_bridge.yaml          # Section 9
├── launch/
│   ├── display.launch.py       # Section 5
│   └── gazebo.launch.py        # Section 9
├── rviz/
│   ├── diffbot.rviz            # Section 5
│   └── diffbot_gazebo.rviz     # Section 9
├── urdf/
│   ├── diffbot_basic.urdf      # Section 4 (Stage 1)
│   ├── diffbot.urdf.xacro      # Sections 6–9 (Stages 2–5)
│   ├── inertial_macros.xacro   # Section 7
│   └── diffbot.gazebo.xacro    # Section 9
└── worlds/
    └── diffbot_world.sdf       # Section 9
```

> **About testing:** The package files in this guide were built and run on Ubuntu 24.04 with ROS 2 Jazzy and Gazebo Harmonic (gz-sim 8.10), installed through the RoboStack conda distribution. The tests used a virtual display and software rendering. They have **not** been tested on the lab computers with the apt packages from Section 2. Instructors should do one full run-through on a lab machine before class.

---

## 2. Set Up the Environment

### 2.1 Install the packages used in this lab

```bash
sudo apt update
sudo apt install -y \
  ros-jazzy-xacro \
  ros-jazzy-robot-state-publisher \
  ros-jazzy-joint-state-publisher-gui \
  ros-jazzy-rviz2 \
  ros-jazzy-tf2-tools \
  ros-jazzy-ros-gz \
  ros-jazzy-teleop-twist-keyboard \
  liburdfdom-tools
```

| Package                               | Provides                                                       |
| ------------------------------------- | -------------------------------------------------------------- |
| `ros-jazzy-xacro`                     | `xacro` command                                                |
| `ros-jazzy-robot-state-publisher`     | URDF → TF                                                      |
| `ros-jazzy-joint-state-publisher-gui` | Joint sliders                                                  |
| `ros-jazzy-tf2-tools`                 | `view_frames`                                                  |
| `ros-jazzy-ros-gz`                    | Gazebo Harmonic (as ROS vendor packages), `ros_gz_sim`, `ros_gz_bridge` |
| `ros-jazzy-teleop-twist-keyboard`     | Keyboard driving                                               |
| `liburdfdom-tools`                    | `check_urdf`, `urdf_to_graphviz`                               |

> **Do not install Gazebo Classic** (`gazebo`, `ros-jazzy-gazebo-ros-pkgs`). On ROS 2 Jazzy, the matching simulator is Gazebo Harmonic, installed by `ros-jazzy-ros-gz`.

### 2.2 Check the installation

Open a new terminal (your `~/.bashrc` from Week 1 should already source ROS 2 and set your `ROS_DOMAIN_ID`):

```bash
echo $ROS_DISTRO
echo $ROS_DOMAIN_ID
gz sim --versions
which check_urdf xacro
```

Expected:

```text
jazzy
<your domain ID>
8.x.x
/usr/bin/check_urdf
/opt/ros/jazzy/bin/xacro
```

`gz sim --versions` must print a version starting with **8** (Gazebo Harmonic).

Quick Gazebo test (close the window when it appears):

```bash
gz sim shapes.sdf
```

**Checkpoint:** `ROS_DISTRO` is `jazzy`, `gz sim --versions` prints 8.x, and the Gazebo window opens.

---

## 3. Create the Package

This package contains only robot files (URDF, launch, configuration), no compiled code. We use the `ament_cmake` build type and install the folders.

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_cmake diffbot_description
cd diffbot_description
rm -rf include src
mkdir -p config launch rviz urdf worlds
```

Replace `~/ros2_ws/src/diffbot_description/CMakeLists.txt` with:

```cmake
cmake_minimum_required(VERSION 3.8)
project(diffbot_description)

find_package(ament_cmake REQUIRED)

# This package contains no compiled code, only files to install.
install(
  DIRECTORY config launch rviz urdf worlds
  DESTINATION share/${PROJECT_NAME}
)

ament_package()
```

Replace `~/ros2_ws/src/diffbot_description/package.xml` with the following (put your own name and e-mail in `<maintainer>`):

```xml
<?xml version="1.0"?>
<?xml-model href="http://download.ros.org/schema/package_format3.xsd" schematypens="http://www.w3.org/2001/XMLSchema"?>
<package format="3">
  <name>diffbot_description</name>
  <version>0.0.0</version>
  <description>Week 2 lab: differential-drive robot description and Gazebo Harmonic simulation</description>
  <maintainer email="you@example.com">your_name</maintainer>
  <license>Apache-2.0</license>

  <buildtool_depend>ament_cmake</buildtool_depend>

  <exec_depend>joint_state_publisher</exec_depend>
  <exec_depend>joint_state_publisher_gui</exec_depend>
  <exec_depend>launch</exec_depend>
  <exec_depend>launch_ros</exec_depend>
  <exec_depend>robot_state_publisher</exec_depend>
  <exec_depend>ros_gz_bridge</exec_depend>
  <exec_depend>ros_gz_sim</exec_depend>
  <exec_depend>rviz2</exec_depend>
  <exec_depend>xacro</exec_depend>

  <export>
    <build_type>ament_cmake</build_type>
  </export>
</package>
```

Build:

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select diffbot_description
source install/setup.bash
ros2 pkg prefix diffbot_description
```

Expected: the last command prints `/home/<user>/ros2_ws/install/diffbot_description`.

> **When to rebuild:** with `--symlink-install`, edits to existing files are picked up without rebuilding. You **must rebuild** (and re-source) after **adding a new file**, or changing `CMakeLists.txt` or `package.xml`.

**Checkpoint:** The package builds and `ros2 pkg prefix diffbot_description` finds it.

---

## 4. Stage 1 — Your First URDF

Robot specification (all units SI):

| Part      | Shape                          | Position relative to `base_link`          |
| --------- | ------------------------------ | ----------------------------------------- |
| Chassis   | Box 0.40 × 0.26 × 0.10 m       | 0.05 m forward, 0.04 m up                 |
| Wheels    | Cylinder r = 0.05 m, w = 0.04 m | y = ±0.16 m                              |
| Caster    | Sphere r = 0.025 m             | 0.18 m forward, touching the ground        |

`base_link` is placed at the centre of the wheel axle. This is the point the robot turns around. `base_footprint` is the same point projected onto the ground, 0.05 m (one wheel radius) lower.

```text
 Side view (x forward →)                      Top view
                                                   y ↑
   ┌──────────────────────┐ chassis           ┌─────────────────┐
   │        +base_link    │                   █ left wheel       │
  (●)───────────────────(○) caster            │   +base_link   ○ │ caster
 ──┴──────────────────────┴── ground           █ right wheel      │
   ↑ base_footprint                           └─────────────────┘──▶ x
```

Create `~/ros2_ws/src/diffbot_description/urdf/diffbot_basic.urdf`:

```xml
<?xml version="1.0"?>
<!-- Stage 1: a plain URDF with visual geometry only. -->
<robot name="diffbot">

  <!-- Ground-projected frame of the robot -->
  <link name="base_footprint"/>

  <!-- Main frame: centre of the drive-wheel axle -->
  <link name="base_link"/>

  <joint name="base_joint" type="fixed">
    <parent link="base_footprint"/>
    <child link="base_link"/>
    <origin xyz="0 0 0.05" rpy="0 0 0"/>
  </joint>

  <!-- Chassis box, shifted forward and up from base_link -->
  <link name="chassis_link">
    <visual>
      <origin xyz="0 0 0" rpy="0 0 0"/>
      <geometry>
        <box size="0.40 0.26 0.10"/>
      </geometry>
      <material name="orange">
        <color rgba="1.0 0.5 0.0 1.0"/>
      </material>
    </visual>
  </link>

  <joint name="chassis_joint" type="fixed">
    <parent link="base_link"/>
    <child link="chassis_link"/>
    <origin xyz="0.05 0 0.04" rpy="0 0 0"/>
  </joint>

  <!-- Left drive wheel -->
  <link name="left_wheel_link">
    <visual>
      <!-- A URDF cylinder is aligned with its local Z axis.
           Rotate it 90 degrees about X so it lies along Y. -->
      <origin xyz="0 0 0" rpy="1.5708 0 0"/>
      <geometry>
        <cylinder radius="0.05" length="0.04"/>
      </geometry>
      <material name="black">
        <color rgba="0.1 0.1 0.1 1.0"/>
      </material>
    </visual>
  </link>

  <joint name="left_wheel_joint" type="continuous">
    <parent link="base_link"/>
    <child link="left_wheel_link"/>
    <origin xyz="0 0.16 0" rpy="0 0 0"/>
    <axis xyz="0 1 0"/>
  </joint>

  <!-- Right drive wheel -->
  <link name="right_wheel_link">
    <visual>
      <origin xyz="0 0 0" rpy="1.5708 0 0"/>
      <geometry>
        <cylinder radius="0.05" length="0.04"/>
      </geometry>
      <material name="black">
        <color rgba="0.1 0.1 0.1 1.0"/>
      </material>
    </visual>
  </link>

  <joint name="right_wheel_joint" type="continuous">
    <parent link="base_link"/>
    <child link="right_wheel_link"/>
    <origin xyz="0 -0.16 0" rpy="0 0 0"/>
    <axis xyz="0 1 0"/>
  </joint>

  <!-- Front caster ball -->
  <link name="caster_link">
    <visual>
      <geometry>
        <sphere radius="0.025"/>
      </geometry>
      <material name="grey">
        <color rgba="0.6 0.6 0.6 1.0"/>
      </material>
    </visual>
  </link>

  <joint name="caster_joint" type="fixed">
    <parent link="base_link"/>
    <child link="caster_link"/>
    <origin xyz="0.18 0 -0.025" rpy="0 0 0"/>
  </joint>

</robot>
```

Things to notice:

* **Joint `origin`** places the child frame: the left wheel frame is 0.16 m to the **left** (+y) of `base_link`.
* **Visual `origin`** only moves or rotates the shape inside its own link. The wheel cylinder is rotated so it lies along y. The link frame itself is not rotated.
* **`axis xyz="0 1 0"`**: the wheels spin about the robot's y (left–right) axis.
* `caster_joint` z = −0.025: the caster centre is 0.025 m above the ground (0.05 − 0.025), so the ball just touches the ground.

### 4.1 Validate the URDF

```bash
cd ~/ros2_ws/src/diffbot_description/urdf
check_urdf diffbot_basic.urdf
```

Expected:

```text
robot name is: diffbot
---------- Successfully Parsed XML ---------------
root Link: base_footprint has 1 child(ren)
    child(1):  base_link
        child(1):  caster_link
        child(2):  chassis_link
        child(3):  left_wheel_link
        child(4):  right_wheel_link
```

Draw the tree as a PDF:

```bash
urdf_to_graphviz diffbot_basic.urdf diffbot_basic
xdg-open diffbot_basic.pdf
```

`urdf_to_graphviz` writes `diffbot_basic.gv` and `diffbot_basic.pdf`. Delete them afterwards (`rm diffbot_basic.gv diffbot_basic.pdf`) so they are not installed with the package.

**Checkpoint:** `check_urdf` prints the tree above with `base_footprint` as the root.

---

## 5. Visualize the Model in RViz2

### 5.1 Display launch file

Create `~/ros2_ws/src/diffbot_description/launch/display.launch.py`:

```python
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument
from launch.conditions import IfCondition
from launch.substitutions import Command, LaunchConfiguration, PathJoinSubstitution
from launch_ros.actions import Node
from launch_ros.parameter_descriptions import ParameterValue
from launch_ros.substitutions import FindPackageShare


def generate_launch_description():
    pkg_share = FindPackageShare('diffbot_description')

    model_arg = DeclareLaunchArgument(
        'model',
        default_value=PathJoinSubstitution([pkg_share, 'urdf', 'diffbot.urdf.xacro']),
        description='Absolute path to the robot URDF or Xacro file',
    )
    gui_arg = DeclareLaunchArgument(
        'gui', default_value='true',
        description='Start joint_state_publisher_gui (sliders) and RViz2',
    )

    # Run xacro on the model file and pass the resulting URDF text as a parameter.
    robot_description = ParameterValue(
        Command(['xacro ', LaunchConfiguration('model')]), value_type=str
    )

    robot_state_publisher = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        parameters=[{'robot_description': robot_description}],
        output='screen',
    )

    joint_state_publisher_gui = Node(
        package='joint_state_publisher_gui',
        executable='joint_state_publisher_gui',
        condition=IfCondition(LaunchConfiguration('gui')),
    )

    rviz = Node(
        package='rviz2',
        executable='rviz2',
        arguments=['-d', PathJoinSubstitution([pkg_share, 'rviz', 'diffbot.rviz'])],
        condition=IfCondition(LaunchConfiguration('gui')),
        output='screen',
    )

    return LaunchDescription([
        model_arg,
        gui_arg,
        robot_state_publisher,
        joint_state_publisher_gui,
        rviz,
    ])
```

`xacro` also accepts plain URDF files, so the same launch file works for Stage 1 and for the Xacro model.

### 5.2 RViz2 configuration

Create `~/ros2_ws/src/diffbot_description/rviz/diffbot.rviz`:

```yaml
Panels:
  - Class: rviz_common/Displays
    Name: Displays
Visualization Manager:
  Class: ""
  Displays:
    - Class: rviz_default_plugins/Grid
      Name: Grid
      Enabled: true
      Reference Frame: <Fixed Frame>
    - Class: rviz_default_plugins/RobotModel
      Name: RobotModel
      Enabled: true
      Description Source: Topic
      Description Topic:
        Value: /robot_description
    - Class: rviz_default_plugins/TF
      Name: TF
      Enabled: true
      Show Names: true
      Show Axes: true
      Show Arrows: true
      Marker Scale: 0.3
  Global Options:
    Fixed Frame: base_footprint
    Frame Rate: 30
  Tools:
    - Class: rviz_default_plugins/MoveCamera
  Views:
    Current:
      Class: rviz_default_plugins/Orbit
      Distance: 1.5
      Focal Point:
        X: 0
        Y: 0
        Z: 0
      Pitch: 0.6
      Yaw: 0.8
```

### 5.3 Launch

New files were added, so rebuild:

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select diffbot_description
source install/setup.bash
ros2 launch diffbot_description display.launch.py \
  model:=$(ros2 pkg prefix --share diffbot_description)/urdf/diffbot_basic.urdf
```

Expected:

* RViz2 shows an orange box, two black wheels, and a grey ball. The **Global Status** is **Ok**.
* The **Joint State Publisher** window has two sliders: `left_wheel_joint` and `right_wheel_joint`.

Verify:

1. Move each slider. The matching wheel should **roll** (rotate about the green **y** axis). It should not spin like a turntable.
2. In the TF display, find `base_footprint` on the ground and `base_link` 0.05 m above it.
3. In another terminal:

```bash
ros2 topic echo /joint_states --once
ros2 run tf2_ros tf2_echo base_link left_wheel_link
```

`tf2_echo` should show `Translation: [0.000, 0.160, 0.000]`. The rotation changes when you move the slider.

**Checkpoint:** The model looks correct in RViz2, and each wheel rolls about the y axis when you move its slider.

---

## 6. Stage 2 — Convert the Model to Xacro

The Stage 1 file repeats the wheel twice and contains many "magic numbers". Xacro fixes both problems.

Create `~/ros2_ws/src/diffbot_description/urdf/diffbot.urdf.xacro`:

```xml
<?xml version="1.0"?>
<robot name="diffbot" xmlns:xacro="http://www.ros.org/wiki/xacro">

  <!-- ===================== Properties (all units SI) ===================== -->
  <xacro:property name="chassis_x" value="0.40"/>
  <xacro:property name="chassis_y" value="0.26"/>
  <xacro:property name="chassis_z" value="0.10"/>
  <xacro:property name="chassis_mass" value="2.0"/>
  <xacro:property name="chassis_offset_x" value="0.05"/>

  <xacro:property name="wheel_radius" value="0.05"/>
  <xacro:property name="wheel_width" value="0.04"/>
  <xacro:property name="wheel_mass" value="0.3"/>
  <xacro:property name="wheel_offset_y" value="0.16"/>

  <xacro:property name="caster_radius" value="0.025"/>
  <xacro:property name="caster_mass" value="0.1"/>
  <xacro:property name="caster_offset_x" value="0.18"/>

  <xacro:property name="lidar_radius" value="0.04"/>
  <xacro:property name="lidar_length" value="0.05"/>
  <xacro:property name="lidar_mass" value="0.1"/>

  <!-- ============================ Materials ============================== -->
  <material name="orange"><color rgba="1.0 0.5 0.0 1.0"/></material>
  <material name="black"><color rgba="0.1 0.1 0.1 1.0"/></material>
  <material name="grey"><color rgba="0.6 0.6 0.6 1.0"/></material>
  <material name="blue"><color rgba="0.2 0.2 0.8 1.0"/></material>

  <!-- ========================= Base frames =============================== -->
  <link name="base_footprint"/>

  <link name="base_link"/>

  <joint name="base_joint" type="fixed">
    <parent link="base_footprint"/>
    <child link="base_link"/>
    <origin xyz="0 0 ${wheel_radius}" rpy="0 0 0"/>
  </joint>

  <!-- ============================ Chassis ================================ -->
  <link name="chassis_link">
    <visual>
      <geometry><box size="${chassis_x} ${chassis_y} ${chassis_z}"/></geometry>
      <material name="orange"/>
    </visual>
    <collision>
      <geometry><box size="${chassis_x} ${chassis_y} ${chassis_z}"/></geometry>
    </collision>
  </link>

  <joint name="chassis_joint" type="fixed">
    <parent link="base_link"/>
    <child link="chassis_link"/>
    <origin xyz="${chassis_offset_x} 0 ${chassis_z / 2 - 0.01}" rpy="0 0 0"/>
  </joint>

  <!-- ========================= Wheel macro =============================== -->
  <!-- reflect = 1 for the left wheel, -1 for the right wheel -->
  <xacro:macro name="drive_wheel" params="prefix reflect">
    <link name="${prefix}_wheel_link">
      <visual>
        <origin xyz="0 0 0" rpy="${pi/2} 0 0"/>
        <geometry><cylinder radius="${wheel_radius}" length="${wheel_width}"/></geometry>
        <material name="black"/>
      </visual>
      <collision>
        <origin xyz="0 0 0" rpy="${pi/2} 0 0"/>
        <geometry><cylinder radius="${wheel_radius}" length="${wheel_width}"/></geometry>
      </collision>
    </link>

    <joint name="${prefix}_wheel_joint" type="continuous">
      <parent link="base_link"/>
      <child link="${prefix}_wheel_link"/>
      <origin xyz="0 ${reflect * wheel_offset_y} 0" rpy="0 0 0"/>
      <axis xyz="0 1 0"/>
    </joint>
  </xacro:macro>

  <xacro:drive_wheel prefix="left" reflect="1"/>
  <xacro:drive_wheel prefix="right" reflect="-1"/>

  <!-- ============================ Caster ================================= -->
  <link name="caster_link">
    <visual>
      <geometry><sphere radius="${caster_radius}"/></geometry>
      <material name="grey"/>
    </visual>
    <collision>
      <geometry><sphere radius="${caster_radius}"/></geometry>
    </collision>
  </link>

  <joint name="caster_joint" type="fixed">
    <parent link="base_link"/>
    <child link="caster_link"/>
    <origin xyz="${caster_offset_x} 0 ${caster_radius - wheel_radius}" rpy="0 0 0"/>
  </joint>

</robot>
```

What changed compared with Stage 1:

| Feature                    | Example                                              |
| -------------------------- | ---------------------------------------------------- |
| Properties                 | `wheel_radius` is defined once and used everywhere  |
| Math                       | `${caster_radius - wheel_radius}`, `${pi/2}`         |
| Macro                      | `drive_wheel` creates a wheel link + joint; `reflect` mirrors it |
| Parametric positions       | `base_joint` z = `${wheel_radius}`: a bigger wheel automatically lifts the robot |
| **Collision** elements     | Same simple shapes as the visuals                    |

(The mass and LiDAR properties are used in Sections 7 and 8.)

### 6.1 Generate and validate

```bash
cd ~/ros2_ws/src/diffbot_description/urdf
xacro diffbot.urdf.xacro > /tmp/diffbot.urdf
check_urdf /tmp/diffbot.urdf
```

Expected: the same tree as Stage 1.

Open the generated file and find where the macro was expanded:

```bash
grep -n "wheel_joint" /tmp/diffbot.urdf
```

### 6.2 View it

```bash
ros2 launch diffbot_description display.launch.py
```

With no `model:=` argument, the launch file uses `diffbot.urdf.xacro`. The robot should look the same as in Stage 1.

**Checkpoint:** `check_urdf` passes on the generated URDF, and RViz2 shows the same robot as Stage 1.

---

## 7. Stage 3 — Inertial Properties

RViz2 does not need mass, but Gazebo does. **A link without a valid `<inertial>` block is removed when Gazebo loads the model**, and its joint is removed with it.

### 7.1 Inertia macros

Create `~/ros2_ws/src/diffbot_description/urdf/inertial_macros.xacro`:

```xml
<?xml version="1.0"?>
<!-- Inertia macros for solid primitive shapes.
     Formulas: Week 2 Lecture Notes, Section 8.4 -->
<robot xmlns:xacro="http://www.ros.org/wiki/xacro">

  <!-- Solid box with size x, y, z -->
  <xacro:macro name="inertial_box" params="mass x y z *origin">
    <inertial>
      <xacro:insert_block name="origin"/>
      <mass value="${mass}"/>
      <inertia ixx="${mass / 12.0 * (y*y + z*z)}" ixy="0.0" ixz="0.0"
               iyy="${mass / 12.0 * (x*x + z*z)}" iyz="0.0"
               izz="${mass / 12.0 * (x*x + y*y)}"/>
    </inertial>
  </xacro:macro>

  <!-- Solid cylinder whose axis is the local Z axis -->
  <xacro:macro name="inertial_cylinder" params="mass radius length *origin">
    <inertial>
      <xacro:insert_block name="origin"/>
      <mass value="${mass}"/>
      <inertia ixx="${mass / 12.0 * (3*radius*radius + length*length)}" ixy="0.0" ixz="0.0"
               iyy="${mass / 12.0 * (3*radius*radius + length*length)}" iyz="0.0"
               izz="${mass / 2.0 * radius*radius}"/>
    </inertial>
  </xacro:macro>

  <!-- Solid sphere -->
  <xacro:macro name="inertial_sphere" params="mass radius *origin">
    <inertial>
      <xacro:insert_block name="origin"/>
      <mass value="${mass}"/>
      <inertia ixx="${2.0 / 5.0 * mass * radius*radius}" ixy="0.0" ixz="0.0"
               iyy="${2.0 / 5.0 * mass * radius*radius}" iyz="0.0"
               izz="${2.0 / 5.0 * mass * radius*radius}"/>
    </inertial>
  </xacro:macro>

</robot>
```

`*origin` is a **block parameter**: the caller passes an `<origin>` element, which is inserted with `<xacro:insert_block name="origin"/>`. The `<origin>` inside `<inertial>` is the **centre of mass** relative to the link frame.

### 7.2 Use the macros

Edit `diffbot.urdf.xacro`:

**a)** Below the opening `<robot …>` line, include the macros:

```xml
  <xacro:include filename="inertial_macros.xacro"/>
```

**b)** In `chassis_link`, after `</collision>`:

```xml
    <xacro:inertial_box mass="${chassis_mass}" x="${chassis_x}" y="${chassis_y}" z="${chassis_z}">
      <origin xyz="0 0 0" rpy="0 0 0"/>
    </xacro:inertial_box>
```

**c)** In the `drive_wheel` macro, after the wheel's `</collision>`. The inertial origin is rotated like the visual, so the cylinder formula's z axis lines up with the wheel's spin axis:

```xml
      <xacro:inertial_cylinder mass="${wheel_mass}" radius="${wheel_radius}" length="${wheel_width}">
        <origin xyz="0 0 0" rpy="${pi/2} 0 0"/>
      </xacro:inertial_cylinder>
```

**d)** In `caster_link`, after `</collision>`:

```xml
    <xacro:inertial_sphere mass="${caster_mass}" radius="${caster_radius}">
      <origin xyz="0 0 0" rpy="0 0 0"/>
    </xacro:inertial_sphere>
```

### 7.3 Check the values

```bash
cd ~/ros2_ws/src/diffbot_description/urdf
xacro diffbot.urdf.xacro > /tmp/diffbot.urdf
check_urdf /tmp/diffbot.urdf
grep -A3 "<inertial>" /tmp/diffbot.urdf | grep -E "mass|inertia "
```

Expected (first values):

```text
      <mass value="2.0"/>
      <inertia ixx="0.012933333333333333" ixy="0.0" ixz="0.0" iyy="0.02833333333333334" iyz="0.0" izz="0.03793333333333333"/>
      <mass value="0.3"/>
      <inertia ixx="0.00022750000000000003" ixy="0.0" ixz="0.0" iyy="0.00022750000000000003" iyz="0.0" izz="0.000375"/>
```

Compare these with your hand calculation in [Kinematics Examples, Example 6](../lecture/kinematics_examples.md#example-6--inertia-of-the-lab-robot).

> **Why not put `<inertial>` on `base_footprint` and `base_link`?** They are empty reference frames connected by fixed joints. The mass belongs to the physical parts (chassis, wheels, caster). Gazebo merges fixed-jointed links and adds up their masses.

**Checkpoint:** The generated URDF has 4 `<inertial>` blocks (chassis, 2 wheels, caster), and the values match your hand calculation.

---

## 8. Stage 4 — Add the LiDAR and Inspect the TF Tree

### 8.1 Add the LiDAR link

In `diffbot.urdf.xacro`, add this block **after the caster joint** and before `</robot>`:

```xml
  <!-- ============================= LiDAR ================================= -->
  <link name="lidar_link">
    <visual>
      <geometry><cylinder radius="${lidar_radius}" length="${lidar_length}"/></geometry>
      <material name="blue"/>
    </visual>
    <collision>
      <geometry><cylinder radius="${lidar_radius}" length="${lidar_length}"/></geometry>
    </collision>
    <xacro:inertial_cylinder mass="${lidar_mass}" radius="${lidar_radius}" length="${lidar_length}">
      <origin xyz="0 0 0" rpy="0 0 0"/>
    </xacro:inertial_cylinder>
  </link>

  <joint name="lidar_joint" type="fixed">
    <parent link="chassis_link"/>
    <child link="lidar_link"/>
    <origin xyz="0.05 0 ${chassis_z / 2 + lidar_length / 2}" rpy="0 0 0"/>
  </joint>
```

The LiDAR sits on top of the chassis: `chassis_z/2` reaches the chassis top surface, and `lidar_length/2` lifts the cylinder's centre above it.

### 8.2 Inspect the TF tree

Terminal 1:

```bash
ros2 launch diffbot_description display.launch.py
```

Terminal 2 — write the TF tree to a PDF:

```bash
cd /tmp
ros2 run tf2_tools view_frames
xdg-open frames_*.pdf
```

Expected tree:

```text
base_footprint
└── base_link
    ├── caster_link
    ├── chassis_link
    │   └── lidar_link
    ├── left_wheel_link
    └── right_wheel_link
```

Terminal 2 — query single transforms:

```bash
ros2 run tf2_ros tf2_echo base_footprint lidar_link
```

Expected:

```text
- Translation: [0.100, 0.000, 0.165]
```

Check this by hand: (0, 0, 0.05) + (0.05, 0, 0.04) + (0.05, 0, 0.075) = (0.10, 0, 0.165). See [Kinematics Examples, Example 3](../lecture/kinematics_examples.md#example-3--chaining-transforms-in-the-lab-robot).

Static vs dynamic transforms:

```bash
ros2 topic echo /tf_static --once     # fixed joints, published once (latched)
ros2 topic echo /tf --once            # moving joints (the wheels), published continuously
```

**Checkpoint:** `view_frames` shows six frames below `base_footprint`, and `tf2_echo` gives (0.100, 0.000, 0.165) for the LiDAR.

---

## 9. Stage 5 — Prepare the Robot for Gazebo Harmonic

Gazebo needs four extra things:

1. **Gazebo settings** in the robot model: friction, a drive plugin, a joint-state plugin, and the LiDAR sensor.
2. A **world** file.
3. A **bridge** configuration to connect Gazebo topics and ROS 2 topics.
4. A **launch file** that starts everything.

### 9.1 Gazebo settings for the robot

Create `~/ros2_ws/src/diffbot_description/urdf/diffbot.gazebo.xacro`:

```xml
<?xml version="1.0"?>
<!-- Gazebo Harmonic settings. ROS tools such as robot_state_publisher ignore <gazebo> tags. -->
<robot xmlns:xacro="http://www.ros.org/wiki/xacro">

  <!-- Low-friction caster so it slides instead of dragging the robot -->
  <gazebo reference="caster_link">
    <mu1>0.001</mu1>
    <mu2>0.001</mu2>
  </gazebo>

  <!-- Colours shown in Gazebo (URDF <material> colours are used by RViz) -->
  <gazebo reference="chassis_link">
    <visual>
      <material>
        <ambient>1.0 0.5 0.0 1</ambient>
        <diffuse>1.0 0.5 0.0 1</diffuse>
      </material>
    </visual>
  </gazebo>

  <gazebo>
    <!-- Differential drive: subscribes to cmd_vel, publishes odom and the odom -> base_footprint TF -->
    <plugin filename="gz-sim-diff-drive-system" name="gz::sim::systems::DiffDrive">
      <left_joint>left_wheel_joint</left_joint>
      <right_joint>right_wheel_joint</right_joint>
      <wheel_separation>${2 * wheel_offset_y}</wheel_separation>
      <wheel_radius>${wheel_radius}</wheel_radius>
      <max_linear_velocity>1.0</max_linear_velocity>
      <max_angular_velocity>2.0</max_angular_velocity>
      <topic>cmd_vel</topic>
      <odom_topic>odom</odom_topic>
      <tf_topic>tf</tf_topic>
      <frame_id>odom</frame_id>
      <child_frame_id>base_footprint</child_frame_id>
      <odom_publish_frequency>30</odom_publish_frequency>
    </plugin>

    <!-- Publishes the positions of the wheel joints -->
    <plugin filename="gz-sim-joint-state-publisher-system" name="gz::sim::systems::JointStatePublisher">
      <topic>joint_states</topic>
    </plugin>
  </gazebo>

  <!-- 2D LiDAR -->
  <gazebo reference="lidar_link">
    <sensor name="lidar" type="gpu_lidar">
      <topic>scan</topic>
      <gz_frame_id>lidar_link</gz_frame_id>
      <update_rate>10</update_rate>
      <always_on>true</always_on>
      <visualize>true</visualize>
      <lidar>
        <scan>
          <horizontal>
            <samples>360</samples>
            <resolution>1</resolution>
            <min_angle>-3.14159</min_angle>
            <max_angle>3.14159</max_angle>
          </horizontal>
        </scan>
        <range>
          <min>0.12</min>
          <max>8.0</max>
          <resolution>0.01</resolution>
        </range>
      </lidar>
    </sensor>
  </gazebo>

</robot>
```

| Block                  | What it does                                                                                  |
| ---------------------- | --------------------------------------------------------------------------------------------- |
| Caster `mu1`/`mu2`     | Almost no friction, so the passive ball slides                                                |
| `DiffDrive` system     | Converts `cmd_vel` (v, ω) into wheel speeds using `wheel_separation` and `wheel_radius`; publishes odometry and the `odom → base_footprint` transform |
| `JointStatePublisher`  | Publishes wheel joint positions, so `robot_state_publisher` can move the wheel frames         |
| `gpu_lidar` sensor     | 360 samples over 360°, 0.12–8 m, 10 Hz, published on `scan` with frame `lidar_link`           |

The plugin values come from the Xacro properties (`${wheel_radius}`), so the controller always matches the model.

Then add the include at the **end** of `diffbot.urdf.xacro`, just before `</robot>`:

```xml
  <!-- ===================== Gazebo-specific settings ====================== -->
  <xacro:include filename="diffbot.gazebo.xacro"/>
```

<details>
<summary><b>Complete <code>diffbot.urdf.xacro</code> after Sections 6–9 (click to expand and compare)</b></summary>

```xml
<?xml version="1.0"?>
<robot name="diffbot" xmlns:xacro="http://www.ros.org/wiki/xacro">

  <xacro:include filename="inertial_macros.xacro"/>

  <!-- ===================== Properties (all units SI) ===================== -->
  <xacro:property name="chassis_x" value="0.40"/>
  <xacro:property name="chassis_y" value="0.26"/>
  <xacro:property name="chassis_z" value="0.10"/>
  <xacro:property name="chassis_mass" value="2.0"/>
  <xacro:property name="chassis_offset_x" value="0.05"/>

  <xacro:property name="wheel_radius" value="0.05"/>
  <xacro:property name="wheel_width" value="0.04"/>
  <xacro:property name="wheel_mass" value="0.3"/>
  <xacro:property name="wheel_offset_y" value="0.16"/>

  <xacro:property name="caster_radius" value="0.025"/>
  <xacro:property name="caster_mass" value="0.1"/>
  <xacro:property name="caster_offset_x" value="0.18"/>

  <xacro:property name="lidar_radius" value="0.04"/>
  <xacro:property name="lidar_length" value="0.05"/>
  <xacro:property name="lidar_mass" value="0.1"/>

  <!-- ============================ Materials ============================== -->
  <material name="orange"><color rgba="1.0 0.5 0.0 1.0"/></material>
  <material name="black"><color rgba="0.1 0.1 0.1 1.0"/></material>
  <material name="grey"><color rgba="0.6 0.6 0.6 1.0"/></material>
  <material name="blue"><color rgba="0.2 0.2 0.8 1.0"/></material>

  <!-- ========================= Base frames =============================== -->
  <link name="base_footprint"/>

  <link name="base_link"/>

  <joint name="base_joint" type="fixed">
    <parent link="base_footprint"/>
    <child link="base_link"/>
    <origin xyz="0 0 ${wheel_radius}" rpy="0 0 0"/>
  </joint>

  <!-- ============================ Chassis ================================ -->
  <link name="chassis_link">
    <visual>
      <geometry><box size="${chassis_x} ${chassis_y} ${chassis_z}"/></geometry>
      <material name="orange"/>
    </visual>
    <collision>
      <geometry><box size="${chassis_x} ${chassis_y} ${chassis_z}"/></geometry>
    </collision>
    <xacro:inertial_box mass="${chassis_mass}" x="${chassis_x}" y="${chassis_y}" z="${chassis_z}">
      <origin xyz="0 0 0" rpy="0 0 0"/>
    </xacro:inertial_box>
  </link>

  <joint name="chassis_joint" type="fixed">
    <parent link="base_link"/>
    <child link="chassis_link"/>
    <origin xyz="${chassis_offset_x} 0 ${chassis_z / 2 - 0.01}" rpy="0 0 0"/>
  </joint>

  <!-- ========================= Wheel macro =============================== -->
  <!-- reflect = 1 for the left wheel, -1 for the right wheel -->
  <xacro:macro name="drive_wheel" params="prefix reflect">
    <link name="${prefix}_wheel_link">
      <visual>
        <origin xyz="0 0 0" rpy="${pi/2} 0 0"/>
        <geometry><cylinder radius="${wheel_radius}" length="${wheel_width}"/></geometry>
        <material name="black"/>
      </visual>
      <collision>
        <origin xyz="0 0 0" rpy="${pi/2} 0 0"/>
        <geometry><cylinder radius="${wheel_radius}" length="${wheel_width}"/></geometry>
      </collision>
      <xacro:inertial_cylinder mass="${wheel_mass}" radius="${wheel_radius}" length="${wheel_width}">
        <origin xyz="0 0 0" rpy="${pi/2} 0 0"/>
      </xacro:inertial_cylinder>
    </link>

    <joint name="${prefix}_wheel_joint" type="continuous">
      <parent link="base_link"/>
      <child link="${prefix}_wheel_link"/>
      <origin xyz="0 ${reflect * wheel_offset_y} 0" rpy="0 0 0"/>
      <axis xyz="0 1 0"/>
    </joint>
  </xacro:macro>

  <xacro:drive_wheel prefix="left" reflect="1"/>
  <xacro:drive_wheel prefix="right" reflect="-1"/>

  <!-- ============================ Caster ================================= -->
  <link name="caster_link">
    <visual>
      <geometry><sphere radius="${caster_radius}"/></geometry>
      <material name="grey"/>
    </visual>
    <collision>
      <geometry><sphere radius="${caster_radius}"/></geometry>
    </collision>
    <xacro:inertial_sphere mass="${caster_mass}" radius="${caster_radius}">
      <origin xyz="0 0 0" rpy="0 0 0"/>
    </xacro:inertial_sphere>
  </link>

  <joint name="caster_joint" type="fixed">
    <parent link="base_link"/>
    <child link="caster_link"/>
    <origin xyz="${caster_offset_x} 0 ${caster_radius - wheel_radius}" rpy="0 0 0"/>
  </joint>

  <!-- ============================= LiDAR ================================= -->
  <link name="lidar_link">
    <visual>
      <geometry><cylinder radius="${lidar_radius}" length="${lidar_length}"/></geometry>
      <material name="blue"/>
    </visual>
    <collision>
      <geometry><cylinder radius="${lidar_radius}" length="${lidar_length}"/></geometry>
    </collision>
    <xacro:inertial_cylinder mass="${lidar_mass}" radius="${lidar_radius}" length="${lidar_length}">
      <origin xyz="0 0 0" rpy="0 0 0"/>
    </xacro:inertial_cylinder>
  </link>

  <joint name="lidar_joint" type="fixed">
    <parent link="chassis_link"/>
    <child link="lidar_link"/>
    <origin xyz="0.05 0 ${chassis_z / 2 + lidar_length / 2}" rpy="0 0 0"/>
  </joint>

  <!-- ===================== Gazebo-specific settings ====================== -->
  <xacro:include filename="diffbot.gazebo.xacro"/>

</robot>
```

</details>

Check that the full model still converts, and look at how Gazebo will see it:

```bash
cd ~/ros2_ws/src/diffbot_description/urdf
xacro diffbot.urdf.xacro > /tmp/diffbot.urdf
check_urdf /tmp/diffbot.urdf
gz sdf -p /tmp/diffbot.urdf > /tmp/diffbot.sdf
grep -E "<link name|<joint name" /tmp/diffbot.sdf
```

Expected SDF links and joints:

```text
    <link name='base_footprint'>
    <joint name='left_wheel_joint' type='revolute'>
    <link name='left_wheel_link'>
    <joint name='right_wheel_joint' type='revolute'>
    <link name='right_wheel_link'>
```

Notice that **only three links remain**. The fixed joints were *lumped*: chassis, caster, and LiDAR are merged into `base_footprint`. This is normal. The `continuous` wheel joints appear as `revolute` joints without limits.

`gz sdf` prints this warning, which you can ignore:

```text
Warning [Utils.cc:132] [...]: XML Element[gz_frame_id], child of element[sensor], not defined in SDF. Copying[gz_frame_id] as children of [sensor].
```

The tag is still copied into the model and used by Gazebo. Section 10.4 checks that the scan is published with `frame_id: lidar_link`.

### 9.2 World file

Create `~/ros2_ws/src/diffbot_description/worlds/diffbot_world.sdf`:

```xml
<?xml version="1.0"?>
<sdf version="1.9">
  <world name="diffbot_world">

    <physics name="1ms" type="ignored">
      <max_step_size>0.001</max_step_size>
      <real_time_factor>1.0</real_time_factor>
    </physics>

    <!-- Core systems: physics, user commands (spawning), scene broadcast, sensors -->
    <plugin filename="gz-sim-physics-system" name="gz::sim::systems::Physics"/>
    <plugin filename="gz-sim-user-commands-system" name="gz::sim::systems::UserCommands"/>
    <plugin filename="gz-sim-scene-broadcaster-system" name="gz::sim::systems::SceneBroadcaster"/>
    <plugin filename="gz-sim-sensors-system" name="gz::sim::systems::Sensors">
      <render_engine>ogre2</render_engine>
    </plugin>

    <light type="directional" name="sun">
      <cast_shadows>true</cast_shadows>
      <pose>0 0 10 0 0 0</pose>
      <diffuse>0.8 0.8 0.8 1</diffuse>
      <specular>0.2 0.2 0.2 1</specular>
      <direction>-0.5 0.1 -0.9</direction>
    </light>

    <model name="ground_plane">
      <static>true</static>
      <link name="link">
        <collision name="collision">
          <geometry>
            <plane><normal>0 0 1</normal><size>100 100</size></plane>
          </geometry>
        </collision>
        <visual name="visual">
          <geometry>
            <plane><normal>0 0 1</normal><size>100 100</size></plane>
          </geometry>
          <material>
            <ambient>0.8 0.8 0.8 1</ambient>
            <diffuse>0.8 0.8 0.8 1</diffuse>
          </material>
        </visual>
      </link>
    </model>

    <!-- Obstacles for the LiDAR -->
    <model name="box_front">
      <static>true</static>
      <pose>2.0 0 0.25 0 0 0</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>0.5 0.5 0.5</size></box></geometry></collision>
        <visual name="visual">
          <geometry><box><size>0.5 0.5 0.5</size></box></geometry>
          <material><ambient>0.2 0.6 0.2 1</ambient><diffuse>0.2 0.6 0.2 1</diffuse></material>
        </visual>
      </link>
    </model>

    <model name="wall_left">
      <static>true</static>
      <pose>0 2.0 0.25 0 0 0</pose>
      <link name="link">
        <collision name="collision"><geometry><box><size>4.0 0.1 0.5</size></box></geometry></collision>
        <visual name="visual">
          <geometry><box><size>4.0 0.1 0.5</size></box></geometry>
          <material><ambient>0.6 0.2 0.2 1</ambient><diffuse>0.6 0.2 0.2 1</diffuse></material>
        </visual>
      </link>
    </model>

  </world>
</sdf>
```

| System             | Why it is needed                                                     |
| ------------------ | -------------------------------------------------------------------- |
| `Physics`          | Simulates motion, gravity, and contacts                              |
| `UserCommands`     | Allows spawning a model while Gazebo runs (used by `ros_gz_sim create`) |
| `SceneBroadcaster` | Sends the scene to the Gazebo GUI                                    |
| `Sensors`          | Renders sensor data. **Without it the LiDAR publishes nothing**      |

### 9.3 Bridge configuration

Gazebo has its own topics (Gazebo Transport), separate from ROS 2 topics. `ros_gz_bridge` copies messages across. Create `~/ros2_ws/src/diffbot_description/config/gz_bridge.yaml`:

```yaml
# ros_gz_bridge configuration: which topics cross between ROS 2 and Gazebo.
- ros_topic_name: "clock"
  gz_topic_name: "clock"
  ros_type_name: "rosgraph_msgs/msg/Clock"
  gz_type_name: "gz.msgs.Clock"
  direction: GZ_TO_ROS

- ros_topic_name: "cmd_vel"
  gz_topic_name: "cmd_vel"
  ros_type_name: "geometry_msgs/msg/Twist"
  gz_type_name: "gz.msgs.Twist"
  direction: ROS_TO_GZ

- ros_topic_name: "odom"
  gz_topic_name: "odom"
  ros_type_name: "nav_msgs/msg/Odometry"
  gz_type_name: "gz.msgs.Odometry"
  direction: GZ_TO_ROS

- ros_topic_name: "tf"
  gz_topic_name: "tf"
  ros_type_name: "tf2_msgs/msg/TFMessage"
  gz_type_name: "gz.msgs.Pose_V"
  direction: GZ_TO_ROS

- ros_topic_name: "joint_states"
  gz_topic_name: "joint_states"
  ros_type_name: "sensor_msgs/msg/JointState"
  gz_type_name: "gz.msgs.Model"
  direction: GZ_TO_ROS

- ros_topic_name: "scan"
  gz_topic_name: "scan"
  ros_type_name: "sensor_msgs/msg/LaserScan"
  gz_type_name: "gz.msgs.LaserScan"
  direction: GZ_TO_ROS
```

Only `cmd_vel` goes **into** Gazebo; everything else comes **out** of Gazebo.

### 9.4 Gazebo launch file

Create `~/ros2_ws/src/diffbot_description/launch/gazebo.launch.py`:

```python
from launch import LaunchDescription
from launch.actions import DeclareLaunchArgument, IncludeLaunchDescription
from launch.conditions import IfCondition
from launch.substitutions import Command, LaunchConfiguration, PathJoinSubstitution
from launch_ros.actions import Node
from launch_ros.parameter_descriptions import ParameterValue
from launch_ros.substitutions import FindPackageShare


def generate_launch_description():
    pkg_share = FindPackageShare('diffbot_description')

    world_arg = DeclareLaunchArgument(
        'world',
        default_value=PathJoinSubstitution([pkg_share, 'worlds', 'diffbot_world.sdf']),
        description='Absolute path to the Gazebo world file',
    )
    rviz_arg = DeclareLaunchArgument(
        'rviz', default_value='true', description='Start RViz2'
    )

    robot_description = ParameterValue(
        Command(['xacro ', PathJoinSubstitution([pkg_share, 'urdf', 'diffbot.urdf.xacro'])]),
        value_type=str,
    )

    # 1. Gazebo Harmonic: load the world and start the simulation (-r = run).
    gazebo = IncludeLaunchDescription(
        PathJoinSubstitution([FindPackageShare('ros_gz_sim'), 'launch', 'gz_sim.launch.py']),
        launch_arguments={
            'gz_args': [LaunchConfiguration('world'), ' -r'],
            'on_exit_shutdown': 'true',
        }.items(),
    )

    # 2. Publish the robot description and the TF of fixed / moving joints.
    robot_state_publisher = Node(
        package='robot_state_publisher',
        executable='robot_state_publisher',
        parameters=[{'robot_description': robot_description, 'use_sim_time': True}],
        output='screen',
    )

    # 3. Spawn the robot in Gazebo from the /robot_description topic.
    spawn_robot = Node(
        package='ros_gz_sim',
        executable='create',
        arguments=['-topic', 'robot_description', '-name', 'diffbot', '-z', '0.02'],
        output='screen',
    )

    # 4. Bridge topics between Gazebo and ROS 2.
    bridge = Node(
        package='ros_gz_bridge',
        executable='parameter_bridge',
        parameters=[{
            'config_file': PathJoinSubstitution([pkg_share, 'config', 'gz_bridge.yaml']),
            'use_sim_time': True,
        }],
        output='screen',
    )

    # 5. Optional RViz2.
    rviz = Node(
        package='rviz2',
        executable='rviz2',
        arguments=['-d', PathJoinSubstitution([pkg_share, 'rviz', 'diffbot_gazebo.rviz'])],
        parameters=[{'use_sim_time': True}],
        condition=IfCondition(LaunchConfiguration('rviz')),
        output='screen',
    )

    return LaunchDescription([
        world_arg,
        rviz_arg,
        gazebo,
        robot_state_publisher,
        spawn_robot,
        bridge,
        rviz,
    ])
```

| Step | Node / include                       | Role                                                                              |
| ---- | ------------------------------------ | --------------------------------------------------------------------------------- |
| 1    | `ros_gz_sim/gz_sim.launch.py`        | Starts Gazebo Harmonic with our world; `-r` starts the simulation unpaused        |
| 2    | `robot_state_publisher`              | Publishes `/robot_description` and the URDF transforms, using **sim time**        |
| 3    | `ros_gz_sim create`                  | Reads `/robot_description` and spawns the model named `diffbot`, 2 cm above ground |
| 4    | `ros_gz_bridge parameter_bridge`     | Bridges the topics listed in `gz_bridge.yaml`                                     |
| 5    | `rviz2`                              | Visualization, using **sim time**                                                 |

### 9.5 RViz2 configuration for the simulation

Create `~/ros2_ws/src/diffbot_description/rviz/diffbot_gazebo.rviz`. It is the same as `diffbot.rviz`, but adds a LaserScan display, uses `odom` as the fixed frame, and zooms out:

```yaml
Panels:
  - Class: rviz_common/Displays
    Name: Displays
Visualization Manager:
  Class: ""
  Displays:
    - Class: rviz_default_plugins/Grid
      Name: Grid
      Enabled: true
      Reference Frame: <Fixed Frame>
    - Class: rviz_default_plugins/RobotModel
      Name: RobotModel
      Enabled: true
      Description Source: Topic
      Description Topic:
        Value: /robot_description
    - Class: rviz_default_plugins/TF
      Name: TF
      Enabled: true
      Show Names: true
      Show Axes: true
      Show Arrows: true
      Marker Scale: 0.3
    - Class: rviz_default_plugins/LaserScan
      Name: LaserScan
      Enabled: true
      Size (m): 0.03
      Topic:
        Value: /scan
  Global Options:
    Fixed Frame: odom
    Frame Rate: 30
  Tools:
    - Class: rviz_default_plugins/MoveCamera
  Views:
    Current:
      Class: rviz_default_plugins/Orbit
      Distance: 6.0
      Focal Point:
        X: 0
        Y: 0
        Z: 0
      Pitch: 0.6
      Yaw: 0.8
```

### 9.6 Build

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select diffbot_description
source install/setup.bash
```

**Checkpoint:** The package builds, and `gz sdf -p` converts the model with three links (`base_footprint` and two wheels).

---

## 10. Run and Verify the Simulation

### 10.1 Launch

```bash
ros2 launch diffbot_description gazebo.launch.py
```

Expected:

* The Gazebo window opens with a grey floor, a green box 2 m ahead, and a red wall on the left.
* The orange robot appears at the origin. The terminal prints `Entity creation successful.`
* RViz2 opens and shows the robot, the TF frames, and red LiDAR points on the box and the wall.
* The terminal prints one `Creating GZ->ROS Bridge` or `Creating ROS->GZ Bridge` line for each of the six bridged topics.

To start without RViz2, use `ros2 launch diffbot_description gazebo.launch.py rviz:=false`.

### 10.2 Check nodes and topics

In a new terminal:

```bash
ros2 node list
ros2 topic list
```

Expected nodes include `/robot_state_publisher` and `/ros_gz_bridge` (plus `/rviz` if enabled). Expected topics include:

```text
/clock
/cmd_vel
/joint_states
/odom
/robot_description
/scan
/tf
/tf_static
```

Check the data rates:

```bash
ros2 topic hz /scan          # configured: 10 Hz
ros2 topic hz /odom          # configured: 30 Hz
ros2 topic echo /clock --once
```

The measured rates can be lower than the configured ones when the computer cannot run the simulation in real time. The rates are counted in wall-clock time. Check the **real-time factor (RTF)** in the bottom-right corner of the Gazebo window: RTF 100% means simulated time runs as fast as real time.

You can also list the Gazebo-side topics and models directly:

```bash
gz topic -l
gz model --list
```

### 10.3 Drive the robot

Terminal — keyboard teleoperation:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Click on this terminal and press `i` (forward), `,` (backward), `j` / `l` (turn), `k` (stop). Use `z` / `x` to reduce or increase the speed.

Or send a command directly (drive in a circle):

```bash
ros2 topic pub -r 10 /cmd_vel geometry_msgs/msg/Twist "{linear: {x: 0.2}, angular: {z: 0.5}}"
```

Stop with `Ctrl+C`, then send zero velocity:

```bash
ros2 topic pub --once /cmd_vel geometry_msgs/msg/Twist "{}"
```

Expected behavior:

| Command                   | Robot motion                                        |
| ------------------------- | --------------------------------------------------- |
| `linear.x > 0`            | Drives forward (+x), straight                       |
| `linear.x < 0`            | Drives backward                                     |
| `angular.z > 0`           | Turns counter-clockwise (left)                      |
| `linear.x = 0.2, angular.z = 0.5` | Circle of radius 0.4 m (v/ω)                |

### 10.4 Check odometry, joints, and the LiDAR

```bash
ros2 topic echo /odom --once --field pose.pose.position
ros2 topic echo /joint_states --once --field position
ros2 topic echo /scan --once --field header
```

Expected `/scan` header:

```text
stamp:
  sec: ...
  nanosec: ...
frame_id: lidar_link
```

With the robot at its start position, the range straight ahead (`ranges[180]`, angle 0) should be about **1.65 m**: the box face is at x = 1.75 m and the LiDAR is at x = 0.10 m. The range to the left (`ranges[270]`, angle +90°) should be about **1.95 m** (the wall).

### 10.5 Check the TF tree in simulation

Simulated data is stamped with **simulation time**, so the TF tools must use it too:

```bash
cd /tmp
ros2 run tf2_tools view_frames --ros-args -p use_sim_time:=true
ros2 run tf2_ros tf2_echo odom lidar_link --ros-args -p use_sim_time:=true
```

Expected tree:

```text
odom
└── base_footprint            (from Gazebo DiffDrive, via the bridge)
    └── base_link             (robot_state_publisher, static)
        ├── caster_link
        ├── chassis_link
        │   └── lidar_link
        ├── left_wheel_link   (robot_state_publisher, from /joint_states)
        └── right_wheel_link
```

> If you run `view_frames` **without** `use_sim_time:=true`, the tree may show only the static frames, with no `odom` and no wheels. The tool's wall clock does not match the simulation timestamps on the moving transforms.

### 10.6 Compare odometry with the model

1. Note `/odom` x.
2. Drive straight for a few seconds.
3. Note `/odom` x and the wheel position in `/joint_states` again.
4. Check: distance travelled ÷ change in wheel angle ≈ **0.05 m** (the wheel radius). See [Kinematics Examples, Example 5](../lecture/kinematics_examples.md#example-5--differential-drive-kinematics).

**Checkpoint:** The robot drives forward, backward, and turns correctly; `/scan` has `frame_id: lidar_link` and sees the box; the TF tree goes from `odom` to all six robot frames.

---

## 11. Troubleshooting

### 11.1 Invalid URDF / Xacro

| Symptom                                                                                         | Cause                                     | Fix                                                                    |
| ----------------------------------------------------------------------------------------------- | ----------------------------------------- | ---------------------------------------------------------------------- |
| `Error: Failed to build tree: parent link [chasis_link] of joint [lidar_joint] not found.`       | Typo in a link name                       | Make the joint's `parent`/`child` match an existing `<link name>`       |
| `Error=XML_ERROR_MISMATCHED_ELEMENT ... Line number=23`                                          | Broken XML (e.g. `</join>`, missing `/`)  | Go to the reported line; check opening and closing tags match          |
| `error: name 'wheel_radus' is not defined when evaluating expression 'wheel_radus'`             | Typo in a Xacro property name             | Spell it as defined in `<xacro:property>`                               |
| `Failed to find root link: Two root links found: [base_footprint] and [caster_link]`             | A joint is missing, so a link has no parent | Every link except the root needs exactly one parent joint            |
| `Joint [left_wheel_joint] is of type REVOLUTE but it does not specify limits`                    | `revolute`/`prismatic` joint without `<limit>` | Add `<limit lower upper effort velocity>`, or use `continuous` for a wheel |
| RViz2: RobotModel status error, robot not shown                                                 | `robot_state_publisher` not running, or wrong fixed frame | Check `ros2 node list`; set Fixed Frame to a frame that exists |

Always validate after every change:

```bash
xacro diffbot.urdf.xacro > /tmp/diffbot.urdf && check_urdf /tmp/diffbot.urdf
```

### 11.2 Missing meshes

This lab uses only primitive shapes. When you use mesh files (for example with the Trailobot model):

| Symptom                                                         | Cause                                             | Fix                                                                                   |
| --------------------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------- |
| RViz2: robot parts missing, "Could not load resource" errors    | Wrong `package://` path, or meshes not installed  | Check the path; add `meshes` to the `install(DIRECTORY ...)` line; rebuild           |
| Gazebo: parts invisible, "Unable to find file" errors           | Gazebo cannot resolve `package://` URIs           | Export the share path: add `<gazebo_ros gazebo_model_path="${prefix}/../"/>` inside `<export>` in `package.xml` (read by `ros_gz_sim`'s `gz_sim.launch.py`), or add the install `share` folder to `GZ_SIM_RESOURCE_PATH` |
| Model is 1000× too big                                          | Mesh exported in millimetres                       | Add `scale="0.001 0.001 0.001"` to `<mesh>` or re-export in metres                   |

### 11.3 Incorrect joint axes

| Symptom                                                    | Cause                                                       | Fix                                                                      |
| ---------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------ |
| Wheel spins like a turntable when you move the slider      | `axis xyz="0 0 1"` while the joint frame is not rotated     | Use `axis xyz="0 1 0"` (this lab's convention)                           |
| Robot drives backward for positive `linear.x`              | Wheel axis is `0 -1 0`                                      | Both wheels use `0 1 0`                                                  |
| Robot only turns, never drives straight (or the reverse)   | One wheel axis flipped, or left/right joints swapped in the plugin | Check `left_joint`/`right_joint` and both axes                     |
| Wheel looks like a flat disc lying down                    | Visual not rotated                                          | `origin rpy="${pi/2} 0 0"` on the wheel visual and collision             |

### 11.4 Bad inertial values

| Symptom                                                                                     | Cause                                       | Fix                                                                         |
| ------------------------------------------------------------------------------------------- | ------------------------------------------- | --------------------------------------------------------------------------- |
| `urdf2sdf: link[left_wheel_link] has no <inertial> block defined` … `is not modeled in sdf` | Missing `<inertial>`                        | Add an inertial macro. Note: **`check_urdf` does not report this.** The robot loads in RViz but has no wheel in Gazebo |
| `has a mass value of less than or equal to zero`                                            | `mass="0"`                                  | Use the real mass (> 0)                                                     |
| Robot jitters, slides, or "explodes" on spawn                                               | Inertia far too small/large, or a very light link on a heavy one | Compute inertia with the macros; use realistic masses        |
| Robot tips over backward/forward                                                             | Centre of mass outside the wheel–caster support area | Move mass forward/back (`chassis_offset_x`), or add a rear caster   |
| Robot does not turn, or drags                                                                 | High caster friction                        | Keep `mu1`/`mu2` small on the caster                                        |

Check what Gazebo will load before you launch:

```bash
gz sdf -p /tmp/diffbot.urdf 2>&1 | grep -iE "warn|error"
```

### 11.5 Missing transforms

| Symptom                                                                     | Cause                                                              | Fix                                                                            |
| --------------------------------------------------------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------------------ |
| `Invalid frame ID "odom" passed to canTransform ... frame does not exist`   | Bridge not running, `tf` missing from `gz_bridge.yaml`, or the tool is not using sim time | `ros2 topic echo /tf`; add `--ros-args -p use_sim_time:=true`               |
| Wheel frames missing in `view_frames` (display launch)                      | No `/joint_states` publisher                                       | Start `joint_state_publisher_gui` (it is in `display.launch.py`)              |
| Wheel frames missing in simulation                                          | `JointStatePublisher` system missing, or `joint_states` not bridged | Check `diffbot.gazebo.xacro` and `gz_bridge.yaml`                             |
| CLI-published `/joint_states` does not move frames                          | Message has no timestamp                                           | Add `header: {stamp: now}` to `ros2 topic pub` (see Kinematics Examples 4.2)   |
| RViz2: "No transform from [lidar_link] to [odom]", or flickering             | RViz2 not using sim time, or two publishers for the same frame     | Start RViz2 with `use_sim_time: True`; publish each frame from one source only |
| `robot_state_publisher`: `Moved backwards in time`                          | Simulation was reset or restarted while nodes kept running         | Restart the launch file                                                        |

### 11.6 Gazebo spawn failures

| Symptom                                                                          | Cause                                                       | Fix                                                                                         |
| -------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| `create` keeps printing `Requesting list of world names.` or waits forever        | Gazebo not running, wrong `-world` name, or different `ROS_DOMAIN_ID`/`GZ_PARTITION` between terminals | Start Gazebo first; do not pass `-world` unless the name matches `<world name>` |
| `create` prints `Waiting messages on topic [robot_description]` forever           | `robot_state_publisher` not running, or its xacro failed     | Check the launch output for xacro errors                                                    |
| `Entity creation successful.` but no second robot appears                         | A model with the same `-name` already exists                | Use a unique `-name`, or `-allow_renaming true`; check `gz model --list`                    |
| Robot falls through the floor                                                     | No `<collision>` on wheels/caster, or the world has no ground plane collision | Add collisions; check the world                                        |
| Robot spawns inside the floor and jumps                                           | Spawn height too low for the model                          | Increase `-z`                                                                               |
| Gazebo window black or crashes in a virtual machine; `Unable to load Ogre Plugin` or `OpenGL 3+` errors | No 3D acceleration / missing OpenGL drivers                 | Enable 3D acceleration in the VM; try `export LIBGL_ALWAYS_SOFTWARE=1` before launching     |
| `/scan` exists but no messages                                                    | `Sensors` system missing from the world                     | Add `gz-sim-sensors-system` to the world file                                               |
| Robot does not move, `/cmd_vel` has a subscriber                                  | `cmd_vel` missing from the bridge, simulation paused, or plugin joint names wrong | Check `gz topic -e -t /cmd_vel` while publishing; press Play in Gazebo; check joint names |

**No working 3D graphics (e.g. a virtual machine)?** Run Gazebo **server-only** with software rendering, and watch the robot in RViz2 instead of the Gazebo window:

```bash
export LIBGL_ALWAYS_SOFTWARE=1
ros2 launch diffbot_description gazebo.launch.py \
  world:="$(ros2 pkg prefix --share diffbot_description)/worlds/diffbot_world.sdf -s --headless-rendering"
```

The `world` value is passed into Gazebo's command line. `-s` starts only the simulation server, and `--headless-rendering` lets the LiDAR render without a window. This is slower than a GPU, but enough for this lab.

### 11.7 General debugging steps

1. Read the **first** error in the terminal, not the last.
2. `xacro … > /tmp/x.urdf && check_urdf /tmp/x.urdf`
3. `gz sdf -p /tmp/x.urdf 2>&1 | grep -iE "warn|error"`
4. `ros2 node list`, `ros2 topic list`, `ros2 topic hz <topic>`
5. `gz topic -l`, `gz model --list`
6. `ros2 run tf2_tools view_frames --ros-args -p use_sim_time:=true`

---

## 12. Final Checklist

* [ ] I installed the lab packages, and `gz sim --versions` prints 8.x.
* [ ] I created the `diffbot_description` package and it builds.
* [ ] `check_urdf` validates my Stage 1 URDF and the generated Xacro URDF.
* [ ] I viewed the robot in RViz2 and moved the wheel joints with sliders.
* [ ] I converted the model to Xacro with properties and a wheel macro.
* [ ] I added collision and inertial properties and checked the inertia values.
* [ ] I added a LiDAR link and inspected the TF tree with `view_frames` and `tf2_echo`.
* [ ] I explained why fixed links are lumped in the SDF.
* [ ] I launched the robot in Gazebo Harmonic with one command.
* [ ] I drove the robot and checked `/odom`, `/joint_states`, `/scan`, and `/clock`.
* [ ] I inspected the full TF tree with `use_sim_time:=true`.

**Next:** Complete the tasks in [exercises.md](exercises.md).
