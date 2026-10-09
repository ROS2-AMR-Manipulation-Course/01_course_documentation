# Week 2 Lecture Notes — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Lecture:** 4 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** [Week 2 README](../README.md) · **Lecture Notes** · [Kinematics Examples](kinematics_examples.md) · [Lab README](../lab/README.md) · [References](../resources/reference_links.md)

---

## Learning Objectives

By the end of this lecture, students should be able to:

1. Describe a robot as a set of **links** connected by **joints**.
2. Distinguish **fixed, revolute, continuous, and prismatic** joints and choose the right one.
3. Count the **degrees of freedom (DoF)** of simple robots.
4. Define **coordinate frames** using the ROS conventions (REP 103 and REP 105).
5. Compose **transformations** (translation + rotation) between frames.
6. Compute **forward kinematics** for a planar arm and a differential-drive robot.
7. Write a **URDF** model with visual, collision, and inertial properties, joint origins, axes, and limits.
8. Use **Xacro** properties, math, macros, and includes to keep a URDF maintainable.
9. Explain how **TF2**, `robot_state_publisher`, and `/joint_states` work together, and inspect a TF tree.
10. Explain how **Gazebo Harmonic** simulates a URDF robot: SDF conversion, physics, collision, inertia, plugins (systems), sensors, the ROS–Gazebo bridge, and simulation time.

### Lecture plan (4 hours)

| Time      | Part | Topic                                                    |
| --------- | ---- | -------------------------------------------------------- |
| 0:00–0:45 | 1    | Robot specification, links, joints, DoF                  |
| 0:45–1:30 | 2    | Coordinate frames, transformations, forward kinematics   |
| 1:30–1:40 | —    | Break                                                    |
| 1:40–2:40 | 3    | URDF and Xacro                                           |
| 2:40–3:10 | 4    | TF2                                                      |
| 3:10–3:20 | —    | Break                                                    |
| 3:20–4:00 | 5    | Gazebo Harmonic: physics, collision, inertia, sensors    |

Worked numerical examples for Parts 1–2 are in [kinematics_examples.md](kinematics_examples.md).

---

# Part 1 — Describing a Robot

## 1. Robot Specification

Before writing any model, write down **what the robot is**. A model is only as good as the specification it comes from.

A minimal specification for a mobile robot:

| Item                 | Example (lab robot `diffbot`)                    |
| -------------------- | ------------------------------------------------ |
| Drive type           | Differential drive, 2 driven wheels + 1 caster   |
| Body dimensions      | 0.40 m × 0.26 m × 0.10 m                         |
| Wheel radius / width | 0.05 m / 0.04 m                                  |
| Wheel separation     | 0.32 m (centre to centre)                        |
| Masses               | Chassis 2.0 kg, each wheel 0.3 kg                |
| Sensors              | 2D LiDAR on top of the chassis                   |
| Limits               | Max 1.0 m/s linear, 2.0 rad/s angular            |

The same table, with the real values, is the starting point for the course robot **Trailobot**.

> **Units:** ROS uses SI units everywhere — metres, kilograms, seconds, radians (REP 103). A model written in millimetres or degrees will load without errors but behave completely wrong.

## 2. Links and Joints

A robot model is a **tree** of rigid bodies.

* A **link** is a rigid body. It has a shape (for drawing), a collision shape (for contact), and mass properties (for physics).
* A **joint** connects exactly one **parent** link to one **child** link and defines how the child may move relative to the parent.

```text
              base_footprint
                    │  base_joint (fixed)
                    ▼
                base_link
      ┌──────────┬──────┴─────┬───────────────┐
      │ fixed    │ continuous │ continuous    │ fixed
      ▼          ▼            ▼               ▼
 chassis_link  left_wheel   right_wheel   caster_link
      │ fixed
      ▼
  lidar_link
```

Rules:

* Every link except the **root** has exactly **one** parent joint.
* Loops are **not allowed** in URDF (a URDF is always a tree).

## 3. Joint Types

| Type           | Motion                              | DoF | Needs `<limit>`?           | Typical use                     |
| -------------- | ----------------------------------- | :-: | -------------------------- | ------------------------------- |
| `fixed`        | None — rigidly attached             |  0  | No                         | Sensors, chassis parts, casters |
| `revolute`     | Rotation about an axis, **bounded** |  1  | **Yes** (lower/upper)      | Robot arm joints, steering      |
| `continuous`   | Rotation about an axis, unbounded   |  1  | No position limits         | Wheels                          |
| `prismatic`    | Translation along an axis, bounded  |  1  | **Yes** (lower/upper in m) | Linear actuators, lifts         |
| `planar`       | Motion in a plane                   |  3  | —                          | Rare                            |
| `floating`     | Free motion in space                |  6  | —                          | Rare                            |

In the URDF specification, `<limit>` is **required** for `revolute` and `prismatic` joints, and its `effort` and `velocity` attributes are required.

```text
 revolute            continuous           prismatic            fixed
   ╭─╮                  ╭─╮
  ( ↻ )  -90°..+90°    ( ↻ )  0..∞        ═══▶  0..0.3 m       ■──■
   ╰─╯                  ╰─╯
```

## 4. Degrees of Freedom (DoF)

The **degrees of freedom** of a system is the number of independent values needed to describe its configuration.

* A free rigid body in 3D: **6 DoF** (x, y, z, roll, pitch, yaw).
* A free rigid body in a plane: **3 DoF** (x, y, θ).
* Each 1-DoF joint (revolute, continuous, prismatic) adds 1 DoF to a serial chain.

| Robot                                | Joint DoF             | Notes                                               |
| ------------------------------------ | --------------------- | --------------------------------------------------- |
| 2-link planar arm                    | 2                     | Two revolute joints                                 |
| Industrial arm (IRB1200, Week 4)     | 6                     | Six revolute joints — any position + orientation    |
| Differential-drive AMR               | 2 wheel joints        | Base pose in the plane has 3 DoF (x, y, θ), but it **cannot move sideways directly** — it is *non-holonomic* |

For planar mechanisms, **Grübler's formula** gives the mobility:

```text
M = 3 (n − 1) − 2 j1 − j2
n  = number of links including the ground
j1 = number of 1-DoF joints
j2 = number of 2-DoF joints
```

For the 2-link arm: n = 3, j1 = 2, j2 = 0 → M = 3·2 − 2·2 = **2**.

---

# Part 2 — Frames, Transformations and Kinematics

## 5. Coordinate Frames

A **coordinate frame** is an origin plus three perpendicular axes. Every link has its own frame. Every sensor reading is expressed in some frame.

### 5.1 ROS conventions — REP 103

* Right-handed axes.
* For a body: **x forward, y left, z up**.
* Colour code in RViz: **x = red, y = green, z = blue**.
* Angles in radians; rotations follow the right-hand rule (thumb along the axis, fingers show positive rotation).

```text
            z (up, blue)
            │
            │
            │_______ y (left, green)
           ╱
          ╱
         x (forward, red)
```

### 5.2 Standard mobile-robot frames — REP 105

```text
map ──▶ odom ──▶ base_footprint ──▶ base_link ──▶ sensor frames
 │        │            │                 │
 │        │            │                 └─ main body frame
 │        │            └─ base projected onto the ground (optional, common)
 │        └─ continuous, drifts slowly; provided by odometry
 └─ global, fixed to the world; provided by localization (Week 3)
```

This week the simulation provides `odom → base_footprint`. In Week 3, SLAM / localization adds `map → odom`.

## 6. Transformations

A transform describes the pose of one frame **relative to** another: a **translation** (x, y, z) and a **rotation**.

### 6.1 Rotation representations

| Representation       | Values                     | Used where                                   |
| -------------------- | -------------------------- | -------------------------------------------- |
| Roll-pitch-yaw (RPY) | 3 angles about x, y, z     | URDF `rpy="r p y"`, human-readable           |
| Quaternion           | (x, y, z, w), unit length  | TF2 and ROS messages — no singularities      |
| Rotation matrix      | 3 × 3 orthonormal matrix   | Mathematics, composing transforms            |

In URDF, `rpy` applies **roll about x, then pitch about y, then yaw about z**, all about the **fixed** (parent) axes.

### 6.2 Homogeneous transforms

A rotation `R` and translation `t` combine into a 4 × 4 matrix:

```text
        ┌         ┐
T_A_B = │  R    t │      p_A = T_A_B · p_B
        │ 0 0 0 1 │
        └         ┘
```

`T_A_B` is "the pose of frame B expressed in frame A". Transforms **chain** by multiplication:

```text
T_A_C = T_A_B · T_B_C
```

This is exactly what TF2 does when you ask for the transform between two frames that are not directly connected.

## 7. Forward Kinematics

**Forward kinematics (FK)** answers: *given the joint values, where is each link?*

### 7.1 Serial arm

For a chain of joints, FK is a product of transforms — one fixed transform (the joint `origin`) and one variable transform (the joint motion) per joint:

```text
T_base_tool = T_origin1 · T_joint1(θ1) · T_origin2 · T_joint2(θ2) · … · T_origin_tool
```

For a planar 2-link arm with link lengths L1, L2:

```text
x = L1 cos θ1 + L2 cos(θ1 + θ2)
y = L1 sin θ1 + L2 sin(θ1 + θ2)
φ = θ1 + θ2              (tool orientation)

          ● tool (x, y)
         ╱ L2
        ●  ← joint 2 (θ2 measured from link 1)
       ╱ L1
      ●──────────▶ x      ← joint 1 (θ1 measured from x)
```

Example: L1 = 0.5 m, L2 = 0.3 m, θ1 = 30°, θ2 = 60° → x = **0.433 m**, y = **0.550 m**, φ = 90°. [kinematics_examples.md](kinematics_examples.md) shows how to check this with TF2.

**Inverse kinematics** (joint values from a desired tool pose) is the harder problem; it is covered in Week 4.

### 7.2 Differential-drive robot

For a robot with wheel radius `r` and wheel separation `W`, the wheel angular speeds `ωL`, `ωR` determine the body velocity:

```text
v = r (ωR + ωL) / 2          linear velocity  (m/s)
ω = r (ωR − ωL) / W          angular velocity (rad/s)
```

Inverting gives the wheel speeds a controller must command:

```text
ωR = (v + ω W/2) / r
ωL = (v − ω W/2) / r
```

For the lab robot (r = 0.05 m, W = 0.32 m), `v = 0.3 m/s, ω = 0` needs both wheels at **6 rad/s**. The Gazebo diff-drive plugin performs exactly this calculation when it receives `/cmd_vel`.

---

# Part 3 — URDF and Xacro

## 8. URDF — Unified Robot Description Format

URDF is an XML format that describes a robot's links and joints. In ROS 2, the URDF text is usually published by `robot_state_publisher` on the `/robot_description` topic and used by RViz2, TF2, Gazebo, Nav2, and MoveIt 2.

### 8.1 Minimal structure

```xml
<?xml version="1.0"?>
<robot name="diffbot">
  <link name="base_link"/>
  <link name="wheel_link"/>
  <joint name="wheel_joint" type="continuous">
    <parent link="base_link"/>
    <child link="wheel_link"/>
    <origin xyz="0 0.16 0" rpy="0 0 0"/>
    <axis xyz="0 1 0"/>
  </joint>
</robot>
```

### 8.2 Inside a link

```xml
<link name="chassis_link">
  <visual>                    <!-- what you SEE (RViz, Gazebo rendering) -->
    <origin xyz="0 0 0" rpy="0 0 0"/>
    <geometry><box size="0.40 0.26 0.10"/></geometry>
    <material name="orange"/>
  </visual>
  <collision>                 <!-- what the physics engine TOUCHES -->
    <origin xyz="0 0 0" rpy="0 0 0"/>
    <geometry><box size="0.40 0.26 0.10"/></geometry>
  </collision>
  <inertial>                  <!-- how the body RESPONDS to forces -->
    <origin xyz="0 0 0" rpy="0 0 0"/>      <!-- centre of mass -->
    <mass value="2.0"/>
    <inertia ixx="0.01293" ixy="0" ixz="0" iyy="0.02833" iyz="0" izz="0.03793"/>
  </inertial>
</link>
```

| Element       | Purpose                                                   | Notes                                                                    |
| ------------- | --------------------------------------------------------- | ------------------------------------------------------------------------ |
| `<visual>`    | Appearance                                                | Can be a detailed mesh (`.dae`, `.stl`, `.obj`)                          |
| `<collision>` | Contact geometry for physics                              | Keep it **simple** (boxes, cylinders, spheres) — faster and more stable  |
| `<inertial>`  | Mass, centre of mass, and inertia tensor                  | **Required for every link Gazebo should simulate as a body**             |
| `<origin>`    | Pose of the element relative to the **link frame**        | Lets the shape sit somewhere other than the link origin                  |

Geometry types: `box size="x y z"`, `cylinder radius length` (axis along the local **z**), `sphere radius`, `mesh filename="package://pkg/meshes/part.stl"`.

### 8.3 Inside a joint

| Element                  | Meaning                                                                                       |
| ------------------------ | --------------------------------------------------------------------------------------------- |
| `parent` / `child`       | The two links connected                                                                       |
| `<origin xyz rpy>`       | Pose of the **child link frame** relative to the **parent link frame** when the joint is at 0 |
| `<axis xyz>`             | Rotation or translation axis, expressed in the **joint (child) frame**; default `1 0 0`       |
| `<limit lower upper effort velocity>` | Position range (rad or m), max effort (N·m or N), max velocity (rad/s or m/s)    |
| `<dynamics damping friction>` | Optional joint damping and friction                                                      |

**Visual origin vs joint origin.** A cylinder's axis is its local z. To make a wheel lie along y you can either:

* rotate the **visual/collision/inertial origins** by `rpy="π/2 0 0"` and keep the joint axis `0 1 0` (used in this course — the joint frame stays aligned with the robot), or
* rotate the **joint origin** and use axis `0 0 1`.

Both are valid; mixing them up is the most common cause of a wheel spinning the wrong way.

### 8.4 Inertial properties

Gazebo needs a **mass** and an **inertia tensor** for every simulated link. For simple shapes about their centre of mass:

| Shape                           | I_xx                    | I_yy                    | I_zz            |
| ------------------------------- | ----------------------- | ----------------------- | --------------- |
| Box (x, y, z sides)             | m(y² + z²)/12           | m(x² + z²)/12           | m(x² + y²)/12   |
| Cylinder (radius r, length h, axis z) | m(3r² + h²)/12    | m(3r² + h²)/12          | m r²/2          |
| Sphere (radius r)               | 2 m r²/5                | 2 m r²/5                | 2 m r²/5        |

Rules of thumb:

* Mass must be **> 0**. A link with zero mass, or no `<inertial>`, is **removed** when Gazebo converts the URDF.
* Use realistic values. Very light links (a few grams) attached to heavy links make the simulation unstable.
* The inertia tensor must be physically valid (positive values; I_xx + I_yy ≥ I_zz and similar for each axis). The formulas above always satisfy this.

## 9. Xacro — XML Macros

Large URDFs repeat themselves (two identical wheels, the same inertia formula). **Xacro** adds programming features and generates the final URDF.

| Feature   | Syntax                                                             | Purpose                         |
| --------- | ------------------------------------------------------------------ | ------------------------------- |
| Namespace | `<robot xmlns:xacro="http://www.ros.org/wiki/xacro">`              | Enables Xacro tags              |
| Property  | `<xacro:property name="wheel_radius" value="0.05"/>`               | Named constant                  |
| Math      | `${2 * wheel_radius}`, `${pi/2}`                                   | Expressions (Python syntax)     |
| Macro     | `<xacro:macro name="wheel" params="prefix reflect"> … </xacro:macro>` | Reusable block with parameters |
| Use macro | `<xacro:wheel prefix="left" reflect="1"/>`                         | Expands the block               |
| Block parameter | `params="mass *origin"` + `<xacro:insert_block name="origin"/>` | Pass an XML element into a macro |
| Include   | `<xacro:include filename="inertial_macros.xacro"/>`                | Split the model into files      |

Generate and check the URDF:

```bash
xacro diffbot.urdf.xacro > /tmp/diffbot.urdf
check_urdf /tmp/diffbot.urdf
```

**Parametric design:** if the base height is written as `${wheel_radius}` instead of `0.05`, changing the wheel size updates every dependent value automatically.

---

# Part 4 — TF2

## 10. TF2 — The Transform Library

TF2 keeps track of all coordinate frames over time and can answer: *"Where is frame B relative to frame A, at time t?"*

```text
                       /joint_states
 joint_state_publisher ─────────────┐
 (or Gazebo)                        ▼
                         ┌──────────────────────┐   /tf_static (fixed joints)
 URDF ──xacro──▶ robot_description ──▶ robot_state_publisher ──▶ /tf (moving joints)
                         └──────────────────────┘   /robot_description
                                                         │
 diff-drive (Gazebo) ── /tf: odom → base_footprint ──────┤
                                                         ▼
                                              RViz2, Nav2, your nodes
```

| Component                | Publishes                                   | Role                                                      |
| ------------------------ | ------------------------------------------- | --------------------------------------------------------- |
| `robot_state_publisher`  | `/robot_description`, `/tf_static`, `/tf`   | Turns URDF + joint positions into transforms (FK!)        |
| `joint_state_publisher_gui` | `/joint_states`                          | Sliders for testing joints without a simulator            |
| Gazebo `JointStatePublisher` system | `/joint_states` (via bridge)     | Real simulated joint positions                            |
| Gazebo `DiffDrive` system | `/odom`, `/tf` (odom → base_footprint)     | Odometry                                                  |

* `/tf_static` carries transforms that never change (fixed joints). It is published once and kept for late subscribers.
* `/tf` carries transforms that change over time (moving joints, odometry).
* Every frame may have **only one parent**. Two publishers for the same child frame cause flickering and errors.

### 10.1 Inspecting TF

```bash
ros2 run tf2_tools view_frames                     # writes frames_<date>.pdf with the whole tree
ros2 run tf2_ros tf2_echo base_link lidar_link     # live transform between two frames
ros2 run tf2_ros tf2_monitor                       # publishing rates and delays
ros2 topic echo /tf_static --once
```

When a simulator is running, add `--ros-args -p use_sim_time:=true` to these tools so they use simulation time (see Section 13).

---

# Part 5 — Physics Simulation with Gazebo Harmonic

## 11. Gazebo Harmonic

Gazebo Harmonic (library `gz-sim` version 8) is the Gazebo release paired with ROS 2 Jazzy. On Jazzy it is installed through the ROS vendor packages, for example with `sudo apt install ros-jazzy-ros-gz`.

> **Not Gazebo Classic.** Older tutorials use `gazebo_ros`, `spawn_entity.py`, `libgazebo_ros_diff_drive.so`, and `.world` files for Gazebo 11. **None of these apply** to Gazebo Harmonic. In Harmonic the command is `gz sim`, ROS integration is the `ros_gz` packages, and plugins are called *systems* with names like `gz-sim-diff-drive-system`.

```text
 ┌──────────────── ROS 2 Jazzy ────────────────┐       ┌──────── Gazebo Harmonic ────────┐
 │ robot_state_publisher ── /robot_description ─┼─────▶ │ ros_gz_sim create (spawn)       │
 │                                              │       │                                 │
 │ teleop / Nav2 ── /cmd_vel ──┐                │       │ Physics (DART), collision        │
 │                             ▼                │       │ Systems: DiffDrive,             │
 │                    ros_gz_bridge ◀──────────┼─────▶ │   JointStatePublisher, Sensors  │
 │ RViz2 ◀── /scan /odom /tf /joint_states /clock│ gz    │ Sensors: gpu_lidar              │
 └──────────────────────────────────────────────┘ topics└─────────────────────────────────┘
```

### 11.1 URDF → SDF

Gazebo's native format is **SDFormat (SDF)**. When you spawn a URDF, Gazebo converts it to SDF automatically. Important consequences:

* Links **without valid inertia are dropped**, together with their parent joint.
* **Fixed joints are merged ("lumped")**: links connected by fixed joints become part of the parent link. In the lab, `chassis_link`, `caster_link`, and `lidar_link` all merge into `base_footprint` inside Gazebo. TF in ROS is not affected, because `robot_state_publisher` still uses the URDF.
* Gazebo-only settings go in `<gazebo>` tags, which ROS tools ignore:

```xml
<gazebo reference="caster_link">   <!-- applies to that link's collisions -->
  <mu1>0.001</mu1>
  <mu2>0.001</mu2>
</gazebo>

<gazebo>                          <!-- applies to the whole model -->
  <plugin filename="gz-sim-diff-drive-system" name="gz::sim::systems::DiffDrive">
    ...
  </plugin>
</gazebo>
```

You can see the converted model yourself with `gz sdf -p robot.urdf`.

### 11.2 The world file

A world (`.sdf`) contains the physics settings, the **systems** the world loads (physics, user commands, scene broadcaster, sensors), lights, the ground plane, and static models.

## 12. Physics: Collision, Inertia, Friction

| Property       | Where                                      | Effect when wrong                                            |
| -------------- | ------------------------------------------ | ------------------------------------------------------------ |
| Collision geometry | `<collision>`                          | Missing → robot falls through the floor or passes through walls |
| Mass / inertia | `<inertial>`                               | Missing/zero → link removed; unrealistic → robot tips, jitters, or flies away |
| Friction       | `<gazebo reference>` `<mu1>`, `<mu2>`      | High caster friction → robot drags or will not turn; low wheel friction → wheels slip |
| Step size      | world `<physics><max_step_size>`           | Too large → unstable contacts                                |
| Joint axis     | `<axis>`                                   | Wrong axis → wheels spin in place or the robot moves sideways |

Gazebo Harmonic uses the **DART** physics engine by default.

## 13. Simulation Time

Gazebo has its own clock, which can run faster or slower than real time (the **real-time factor**) or be paused.

* Gazebo publishes its time; the bridge forwards it to ROS as `/clock`.
* Every ROS node that works with simulated data must run with **`use_sim_time:=true`**.
* If a node uses wall-clock time while the data uses simulation time, TF lookups fail with "frame does not exist" or "extrapolation" errors, because the timestamps do not match.

## 14. Sensors in Simulation

Gazebo simulates sensors such as LiDAR (`gpu_lidar`), cameras, IMUs, and contact sensors. A sensor needs:

1. A `<sensor>` element on a link (via `<gazebo reference="lidar_link">`).
2. The **Sensors system** loaded in the world (`gz-sim-sensors-system`), which renders the sensor data.
3. A bridge entry to bring the Gazebo topic (e.g. `scan`) into ROS as `sensor_msgs/msg/LaserScan`.

The `<gz_frame_id>` tag sets the `frame_id` in the published messages, so the data lines up with the TF frame `lidar_link`.

---

## Summary

| Concept           | Key idea                                                                   |
| ----------------- | -------------------------------------------------------------------------- |
| Links / joints    | Rigid bodies connected in a tree; each joint has one parent and one child  |
| Joint types       | fixed 0 DoF; revolute, continuous, prismatic 1 DoF each                    |
| Frames            | x forward, y left, z up; `map → odom → base_footprint → base_link`         |
| Transforms        | Translation + rotation; chain by multiplication                            |
| Forward kinematics| Joint values → link poses; computed by `robot_state_publisher`             |
| URDF              | visual (see), collision (touch), inertial (move); joint origin, axis, limit |
| Xacro             | properties, math, macros, includes → generates URDF                        |
| TF2               | Tracks all frames over time; `/tf` and `/tf_static`                        |
| Gazebo Harmonic   | URDF → SDF, physics + systems + sensors; `ros_gz_bridge`; `use_sim_time`  |

## Glossary

| Term                | Meaning                                                                 |
| ------------------- | ----------------------------------------------------------------------- |
| DoF                 | Number of independent values describing a configuration                 |
| FK / IK             | Forward / inverse kinematics                                            |
| Holonomic           | Can move instantly in any direction of its configuration space          |
| Lumping             | Merging links joined by fixed joints during URDF → SDF conversion       |
| SDF / SDFormat      | Gazebo's native description format                                      |
| System (Gazebo)     | A plugin that adds behavior to a world or model                         |
| Real-time factor    | Simulated time ÷ wall-clock time                                        |

## Self-check Questions

1. Why does a drive wheel use a `continuous` joint instead of a `revolute` joint?
2. A robot arm has 3 revolute joints and 1 prismatic joint in series. How many DoF does it have?
3. What is the difference between a joint's `<origin>` and a visual's `<origin>`?
4. Why should collision geometry usually be simpler than visual geometry?
5. What happens in Gazebo Harmonic to a link that has no `<inertial>` block?
6. Which node turns `/joint_states` into `/tf`?
7. Why must RViz2 use `use_sim_time:=true` when it shows simulated data?
8. Name the three things needed to get simulated LiDAR data into ROS 2.

**Next:** [Kinematics Examples](kinematics_examples.md) → then the [Week 2 Lab](../lab/README.md).
