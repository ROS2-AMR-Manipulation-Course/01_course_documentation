# Week 2 — Kinematics Worked Examples

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Use with:** [Lecture Notes](notes.md) Parts 1–2, and the lab
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy

**Navigation:** [Week 2 README](../README.md) · [Lecture Notes](notes.md) · **Kinematics Examples** · [Lab Guide](../lab/lab_guide.md)

---

Each example shows the hand calculation first. Where possible, it then shows how to check the answer with ROS 2 tools. The numbers use the lab robot `diffbot` and the small `two_link_arm` model defined in Example 4.

All angles are in **radians** in ROS. Conversions used below: 30° = 0.5236 rad, 60° = 1.0472 rad, 90° = 1.5708 rad.

---

## Example 1 — Counting Degrees of Freedom

| Robot                                       | Joints                               | DoF                                   |
| ------------------------------------------- | ------------------------------------ | ------------------------------------- |
| `two_link_arm`                              | 2 revolute                           | 2                                     |
| SCARA-style arm                             | 3 revolute + 1 prismatic             | 4                                     |
| 6-axis industrial arm (Week 4)              | 6 revolute                           | 6                                     |
| `diffbot`                                   | 2 continuous (wheels), 2 fixed       | 2 joint DoF; base pose (x, y, θ) = 3, but non-holonomic |

**Grübler check for `two_link_arm`** (planar): n = 3 bodies including the ground, j1 = 2 one-DoF joints.

```text
M = 3 (n − 1) − 2 j1 − j2 = 3 · 2 − 2 · 2 − 0 = 2
```

**Why is `diffbot` non-holonomic?** Its base has 3 DoF in the plane, but only 2 inputs (the wheels), and the wheels cannot roll sideways. It can still reach any (x, y, θ) — but not by moving sideways directly; it must turn and drive, like parallel parking.

---

## Example 2 — Rotating and Translating a Point

Frame **B** is rotated **90° about z** relative to frame **A** and its origin is at (1, 0, 0) in A. A point is at `p_B = (0.5, 0, 0)` in frame B. Where is it in frame A?

Rotation about z by θ:

```text
       ┌ cos θ  −sin θ  0 ┐            ┌ 0 −1 0 ┐
Rz(θ) =│ sin θ   cos θ  0 │  θ = 90° → │ 1  0 0 │
       └   0       0    1 ┘            └ 0  0 1 ┘
```

```text
p_A = R · p_B + t = (0·0.5 − 1·0,  1·0.5 + 0·0,  0) + (1, 0, 0) = (1.0, 0.5, 0)
```

**Answer:** `p_A = (1.0, 0.5, 0)`. The point lies 0.5 m "ahead" of B, and B's x axis points along A's y axis.

---

## Example 3 — Chaining Transforms in the Lab Robot

Where is the LiDAR relative to the robot's ground frame `base_footprint`? All joints on this path are fixed and have no rotation, so the translations simply add:

| Joint           | Parent → child                | `origin xyz`                       |
| --------------- | ----------------------------- | ---------------------------------- |
| `base_joint`    | base_footprint → base_link    | (0, 0, 0.05)  — wheel radius       |
| `chassis_joint` | base_link → chassis_link      | (0.05, 0, 0.04)                    |
| `lidar_joint`   | chassis_link → lidar_link     | (0.05, 0, 0.075) — half chassis height + half LiDAR height |

```text
T_footprint_lidar = T_footprint_base · T_base_chassis · T_chassis_lidar
translation       = (0, 0, 0.05) + (0.05, 0, 0.04) + (0.05, 0, 0.075) = (0.10, 0, 0.165)
```

**Check with TF2** (with the lab simulation running — see [Lab Guide Section 10](../lab/lab_guide.md#10-run-and-verify-the-simulation)):

```bash
ros2 run tf2_ros tf2_echo base_footprint lidar_link --ros-args -p use_sim_time:=true
```

Expected:

```text
- Translation: [0.100, 0.000, 0.165]
- Rotation: in Quaternion (xyzw) [0.000, 0.000, 0.000, 1.000]
```

### 3.1 Adding a rotation: a LiDAR hit in the `odom` frame

The robot is at (1.0, 2.0) in `odom`, facing **+y** (yaw = 90°). The LiDAR measures an obstacle **1.65 m straight ahead**. Where is the obstacle in `odom`?

1. In `lidar_link`: (1.65, 0, 0).
2. In `base_footprint`: add the LiDAR offset → (1.75, 0, 0.165).
3. In `odom`: rotate by 90° about z, `(x, y) → (−y, x)` = (0, 1.75), then add the robot position (1.0, 2.0):

```text
p_odom = (1.0, 3.75, 0.165)
```

This is the calculation TF2 performs automatically when Nav2 or SLAM transforms a laser scan in Week 3.

---

## Example 4 — Forward Kinematics of a 2-Link Planar Arm

Link lengths L1 = 0.5 m, L2 = 0.3 m. Both joints rotate about z. The base is 0.1 m tall.

```text
x = L1 cos θ1 + L2 cos(θ1 + θ2)
y = L1 sin θ1 + L2 sin(θ1 + θ2)
z = 0.1
φ = θ1 + θ2
```

| θ1   | θ2   | x (m)  | y (m) | Tool yaw φ |
| ---: | ---: | -----: | ----: | ---------: |
| 0°   | 0°   | 0.800  | 0.000 | 0°         |
| 30°  | 60°  | 0.433  | 0.550 | 90°        |
| 90°  | −90° | 0.300  | 0.500 | 0°         |
| 45°  | 45°  | 0.354  | 0.654 | 90°        |
| 120° | −30° | −0.250 | 0.733 | 90°        |

Worked line 2: x = 0.5 · cos 30° + 0.3 · cos 90° = 0.5 · 0.866 + 0 = **0.433**; y = 0.5 · sin 30° + 0.3 · sin 90° = 0.25 + 0.3 = **0.550**.

### 4.1 The arm as a URDF

Save as `two_link_arm.urdf.xacro` (for example in the `urdf/` folder of the lab package):

```xml
<?xml version="1.0"?>
<robot name="two_link_arm" xmlns:xacro="http://www.ros.org/wiki/xacro">

  <xacro:property name="L1" value="0.5"/>
  <xacro:property name="L2" value="0.3"/>
  <xacro:property name="base_height" value="0.1"/>

  <link name="base_link">
    <visual>
      <origin xyz="0 0 ${base_height / 2}"/>
      <geometry><cylinder radius="0.08" length="${base_height}"/></geometry>
    </visual>
  </link>

  <link name="link1">
    <visual>
      <origin xyz="${L1 / 2} 0 0"/>
      <geometry><box size="${L1} 0.04 0.04"/></geometry>
    </visual>
  </link>

  <link name="link2">
    <visual>
      <origin xyz="${L2 / 2} 0 0"/>
      <geometry><box size="${L2} 0.03 0.03"/></geometry>
    </visual>
  </link>

  <link name="tool0"/>

  <joint name="joint1" type="revolute">
    <parent link="base_link"/>
    <child link="link1"/>
    <origin xyz="0 0 ${base_height}" rpy="0 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="${-pi}" upper="${pi}" effort="10.0" velocity="1.0"/>
  </joint>

  <joint name="joint2" type="revolute">
    <parent link="link1"/>
    <child link="link2"/>
    <origin xyz="${L1} 0 0" rpy="0 0 0"/>
    <axis xyz="0 0 1"/>
    <limit lower="${-pi / 2}" upper="${pi / 2}" effort="10.0" velocity="1.0"/>
  </joint>

  <joint name="tool_joint" type="fixed">
    <parent link="link2"/>
    <child link="tool0"/>
    <origin xyz="${L2} 0 0" rpy="0 0 0"/>
  </joint>

</robot>
```

Notice how the **joint origins encode the link lengths**: `joint2` sits at `x = L1` in `link1`'s frame, and `tool0` sits at `x = L2` in `link2`'s frame. The visual boxes are shifted by half their length so they start at the joint.

### 4.2 Check the FK with TF2

Terminal 1 — publish the model (no simulator needed):

```bash
ros2 run robot_state_publisher robot_state_publisher --ros-args \
  -p robot_description:="$(xacro two_link_arm.urdf.xacro)"
```

Terminal 2 — set θ1 = 30°, θ2 = 60°:

```bash
ros2 topic pub -r 10 /joint_states sensor_msgs/msg/JointState \
  "{header: {stamp: now}, name: [joint1, joint2], position: [0.5236, 1.0472]}"
```

> **Include `header: {stamp: now}`.** Without a timestamp, `robot_state_publisher` does not publish the moving-joint transforms.

Terminal 3:

```bash
ros2 run tf2_ros tf2_echo base_link tool0
```

Expected:

```text
- Translation: [0.433, 0.550, 0.100]
- Rotation: in RPY (degree) [0.000, -0.000, 90.000]
```

The values match the hand calculation. `robot_state_publisher` is a forward-kinematics engine. Instead of publishing joint values from the CLI, you can also use the sliders: `ros2 run joint_state_publisher_gui joint_state_publisher_gui`.

---

## Example 5 — Differential-Drive Kinematics

Lab robot: wheel radius r = 0.05 m, wheel separation W = 0.32 m.

```text
Body velocity from wheel speeds:     v = r (ωR + ωL) / 2       ω = r (ωR − ωL) / W
Wheel speeds from body velocity:     ωR = (v + ω W/2) / r      ωL = (v − ω W/2) / r
```

### 5.1 From commands to wheel speeds

| Command `/cmd_vel`              | ωR (rad/s) | ωL (rad/s) | Motion                         |
| ------------------------------- | ---------: | ---------: | ------------------------------ |
| v = 0.3 m/s, ω = 0              |       6.00 |       6.00 | Straight ahead                 |
| v = 0, ω = 0.5 rad/s            |       1.60 |      −1.60 | Turn in place, counter-clockwise |
| v = 0.2 m/s, ω = 0.5 rad/s      |       5.60 |       2.40 | Arc to the left                |
| v = 0.2 m/s, ω = −1.0 rad/s     |       0.80 |       7.20 | Tight arc to the right         |

Worked line 3: ωR = (0.2 + 0.5 · 0.16) / 0.05 = 0.28 / 0.05 = **5.6 rad/s**; ωL = (0.2 − 0.08) / 0.05 = **2.4 rad/s**.

### 5.2 From wheel speeds to body motion

ωL = 4 rad/s, ωR = 8 rad/s:

```text
v = 0.05 · (8 + 4) / 2  = 0.30 m/s
ω = 0.05 · (8 − 4) / 0.32 = 0.625 rad/s     (turning left)
radius of the arc R = v / ω = 0.48 m
```

### 5.3 Distance and wheel angle

A wheel that rolls without slipping turns by `Δθ = d / r`. If the robot drives 0.844 m straight, each wheel turns `0.844 / 0.05 = 16.88 rad` (about 2.7 revolutions).

**Check in the simulation:** drive straight, then compare `/odom` with `/joint_states`:

```bash
ros2 topic echo /odom --once --field pose.pose.position
ros2 topic echo /joint_states --once --field position
```

Distance ÷ wheel angle should be close to the wheel radius, 0.05 m.

---

## Example 6 — Inertia of the Lab Robot

| Link           | Shape     | Mass (kg) | Size (m)                 | I_xx       | I_yy       | I_zz       |
| -------------- | --------- | --------: | ------------------------ | ---------: | ---------: | ---------: |
| `chassis_link` | Box       | 2.0       | 0.40 × 0.26 × 0.10       | 0.012933   | 0.028333   | 0.037933   |
| wheel (each)   | Cylinder  | 0.3       | r = 0.05, h = 0.04       | 0.0002275  | 0.0002275  | 0.000375   |
| `caster_link`  | Sphere    | 0.1       | r = 0.025                | 0.000025   | 0.000025   | 0.000025   |
| `lidar_link`   | Cylinder  | 0.1       | r = 0.04, h = 0.05       | 0.0000608  | 0.0000608  | 0.00008    |

Worked chassis I_zz: `m (x² + y²) / 12 = 2.0 · (0.16 + 0.0676) / 12 = 0.037933 kg·m²`.

Worked wheel: `I_xx = I_yy = 0.3 · (3 · 0.0025 + 0.0016) / 12 = 0.0002275`; `I_zz = 0.3 · 0.0025 / 2 = 0.000375`. The cylinder formula uses the cylinder's own z axis. The lab rotates the wheel's inertial origin by `rpy="π/2 0 0"`, so the largest value (0.000375) acts about the wheel's spin axis (the robot's y axis).

Total robot mass: 2.0 + 2 × 0.3 + 0.1 + 0.1 = **2.8 kg**.

**Check:** run `xacro diffbot.urdf.xacro` and compare the generated `<inertia>` values with this table.

---

## Practice Problems

1. θ1 = 0°, θ2 = 90° for the 2-link arm. Find (x, y) and the tool yaw.
2. Which `/cmd_vel` makes `diffbot` drive an arc of radius 1.0 m to the right at 0.25 m/s? Give v, ω, ωL, and ωR.
3. The wheel radius changes to 0.07 m. Which `origin` values in the lab Xacro change automatically, and what is the new height of `lidar_link` above the ground?
4. A prismatic joint along z is added on top of `tool0` with `lower="0" upper="0.2"`. How many DoF does the arm now have? What is the tool position for θ1 = 30°, θ2 = 60°, d = 0.15 m?

### Answers

1. x = 0.5 + 0 = **0.500**, y = 0 + 0.3 = **0.300**, yaw = **90°**.
2. Turning right means ω < 0: v = 0.25 m/s, ω = −v/R = **−0.25 rad/s**. ωR = (0.25 − 0.04)/0.05 = **4.2 rad/s**, ωL = (0.25 + 0.04)/0.05 = **5.8 rad/s**.
3. `base_joint` z (= `wheel_radius`) and `caster_joint` z (= `caster_radius − wheel_radius`) update automatically; the chassis and LiDAR offsets are relative to `base_link`, so they move up with it. New LiDAR height = 0.07 + 0.04 + 0.075 = **0.185 m**.
4. **3 DoF**. The prismatic joint adds 0.15 m along the tool z axis, which is still world z for this planar arm: (0.433, 0.550, **0.250**).
