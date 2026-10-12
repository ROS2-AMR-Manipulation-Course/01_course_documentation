# Week 2 Lecture — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Lecture:** 4 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** [Week 2 README](../README.md) · **Lecture README** · [Lecture Notes](notes.md) · [Kinematics Examples](kinematics_examples.md) · [Lab](../lab/README.md)

---

## 1. Lecture Topic

How do we describe a robot so that software can reason about its shape, motion, and physics? This lecture moves from the physical robot (links and joints) to the mathematics (frames, transforms, forward kinematics), then to the ROS 2 robot description (URDF, Xacro, TF2), and finally to physics simulation in Gazebo Harmonic.

## 2. Learning Objectives

By the end of the lecture, students should be able to:

1. Identify links, joints (fixed, revolute, continuous, prismatic), and degrees of freedom.
2. Define coordinate frames using REP 103 and REP 105.
3. Compose transformations and compute forward kinematics for a planar arm and a differential-drive robot.
4. Read and write URDF links (visual, collision, inertial) and joints (origin, axis, limit).
5. Use Xacro properties, expressions, macros, and includes.
6. Explain how `robot_state_publisher`, `/joint_states`, and TF2 work together.
7. Explain how Gazebo Harmonic simulates a URDF robot: SDF conversion, physics, inertia, sensors, the ROS–Gazebo bridge, and simulation time.

## 3. Lecture Materials

| Material                                    | Description                                                                 |
| ------------------------------------------- | --------------------------------------------------------------------------- |
| Slides                                      | [`Week2 -Kinematic_Modeling_&_Physics_Simulation.pdf`](Week2%20-Kinematic_Modeling_%26_Physics_Simulation.pdf), uploaded by the course maintainer |
| [Lecture notes](notes.md)                   | Full written notes for all five parts, with diagrams, summary, and glossary |
| [Kinematics examples](kinematics_examples.md) | Worked examples (transforms, FK, differential drive, inertia) with TF2 checks and practice problems |

**About the slides:** the slides are the visual summary used in class. The lecture notes are the complete written reference, and students should read them after the lecture. Slides are prepared and uploaded by the course maintainer. If a `slides.pdf` file is added later, it is the current version of the slides.

## 4. Lecture Plan (4 hours)

| Time      | Part | Topic                                                  | Notes section |
| --------- | ---- | ------------------------------------------------------ | ------------- |
| 0:00–0:45 | 1    | Robot specification, links, joints, DoF                | [Part 1](notes.md#part-1--describing-a-robot) |
| 0:45–1:30 | 2    | Coordinate frames, transformations, forward kinematics | [Part 2](notes.md#part-2--frames-transformations-and-kinematics) |
| 1:30–1:40 | —    | Break                                                  |               |
| 1:40–2:40 | 3    | URDF and Xacro                                         | [Part 3](notes.md#part-3--urdf-and-xacro) |
| 2:40–3:10 | 4    | TF2                                                    | [Part 4](notes.md#part-4--tf2) |
| 3:10–3:20 | —    | Break                                                  |               |
| 3:20–4:00 | 5    | Gazebo Harmonic: physics, collision, inertia, sensors  | [Part 5](notes.md#part-5--physics-simulation-with-gazebo-harmonic) |

## 5. Connection to the Course Robots

* **Trailobot** has the same structure as the lab robot (two continuous wheel joints, casters, LiDAR frames) — see the [Trailobot platform page](../../../robot_platforms/trailobot_amr.md).
* **ABB IRB 1200**: its URDF joint chain (origins, axes, limits) is a real example of the serial-arm FK in Part 2 — see the [IRB 1200 platform page](../../../robot_platforms/abb_irb1200_manipulator.md).

---

## 6. Review Quiz

Ten questions on the lecture. Allow 25 minutes; a calculator is allowed. Instructors may use the quiz for the course's "weekly exercises / quizzes" component ([assessment policy](../../../assessment_policy.md)). Students can also use it to check themselves. Try the questions **before** opening the answers.

### Question 1 — Joint types

A robot's drive wheel must be able to rotate forever in both directions. Which URDF joint type should it use?

* A. `fixed`
* B. `revolute`
* C. `continuous`
* D. `prismatic`

---

### Question 2 — Degrees of freedom

A planar arm has three links connected in series by **two revolute joints**, with the first link fixed to the ground. How many degrees of freedom does it have?

* A. 1
* B. 2
* C. 3
* D. 6

---

### Question 3 — Coordinate frame conventions

According to ROS conventions (REP 103), the body frame of a mobile robot has:

* A. x forward, y left, z up
* B. x forward, y right, z down
* C. x right, y forward, z up
* D. x up, y left, z forward

---

### Question 4 — Visual, collision, inertial

Match each URDF element to its purpose:

| Element         | Purpose (write a, b, or c) |
| --------------- | -------------------------- |
| `<visual>`      |                            |
| `<collision>`   |                            |
| `<inertial>`    |                            |

* a. Mass, centre of mass, and moments of inertia used to compute motion
* b. Geometry used to compute contact with other objects
* c. Geometry drawn in RViz2 and the Gazebo GUI

---

### Question 5 — Inertia calculation (short answer)

A chassis is modeled as a solid box with mass m = 2.0 kg and size x = 0.40 m, y = 0.26 m, z = 0.10 m. Calculate **I_zz** about its centre of mass. Show your formula.

---

### Question 6 — Forward kinematics (short answer)

A planar 2-link arm has L1 = 0.5 m and L2 = 0.3 m. Both joints rotate about z. With θ1 = 90° and θ2 = −90°, what is the (x, y) position of the tool, and what is its orientation φ?

---

### Question 7 — Missing inertia

A student's URDF passes `check_urdf`. In RViz2 the robot looks correct. When it is spawned in Gazebo Harmonic, the left wheel is missing, and the conversion prints `link[left_wheel_link] has no <inertial> block defined`. What is the most likely cause, and what happens to the wheel's joint?

* A. The mesh file is missing; the joint becomes fixed
* B. The left wheel link has no `<inertial>` block; the link and its parent joint are left out of the simulated model
* C. The wheel's `<collision>` is missing; the joint is unaffected
* D. The joint axis is wrong; the joint is converted to prismatic

---

### Question 8 — TF2

Which statement about `robot_state_publisher` is correct?

* A. It reads `/cmd_vel` and publishes the robot's odometry.
* B. It publishes `/joint_states` from the URDF joint limits.
* C. It reads the URDF and `/joint_states` and publishes the transforms of fixed joints on `/tf_static` and of moving joints on `/tf`.
* D. It converts the URDF to SDF and spawns the robot in Gazebo.

---

### Question 9 — Simulation time (short answer)

The simulation is running. A student runs `ros2 run tf2_tools view_frames`. The PDF shows only `base_footprint`, `base_link`, `chassis_link`, `caster_link`, and `lidar_link`. The frames `odom`, `left_wheel_link`, and `right_wheel_link` are missing, but `ros2 topic echo /tf` shows these transforms being published. Explain the cause and give the corrected command.

---

### Question 10 — ROS–Gazebo bridge

In the `ros_gz_bridge` configuration, which `direction` is correct for each topic?

| Topic          | Direction (`ROS_TO_GZ`, `GZ_TO_ROS`, or `BIDIRECTIONAL`) |
| -------------- | -------------------------------------------------------- |
| `/cmd_vel`     |                                                          |
| `/scan`        |                                                          |
| `/clock`       |                                                          |

<details>
<summary><b>Answers and explanations</b> (open after attempting the quiz)</summary>

### Answer Summary

| Q  | Answer                                                                  | Topic                      |
| -- | ----------------------------------------------------------------------- | -------------------------- |
| 1  | C                                                                       | Joint types                |
| 2  | B                                                                       | DoF                        |
| 3  | A                                                                       | REP 103                    |
| 4  | visual → c, collision → b, inertial → a                                 | Link elements              |
| 5  | I_zz ≈ 0.038 kg·m² (0.037933)                                           | Inertia                    |
| 6  | (0.300, 0.500), φ = 0°                                                  | Forward kinematics         |
| 7  | B                                                                       | Inertia in Gazebo          |
| 8  | C                                                                       | TF2 / robot_state_publisher |
| 9  | Tool not using sim time; add `--ros-args -p use_sim_time:=true`         | Simulation time            |
| 10 | `/cmd_vel` ROS_TO_GZ; `/scan` GZ_TO_ROS; `/clock` GZ_TO_ROS              | ros_gz_bridge              |

### Explanations

#### Question 1 — C (`continuous`)

A `continuous` joint rotates about its axis without position limits, which is what a wheel needs. `revolute` also rotates but **requires** `lower`/`upper` limits. `fixed` does not move. `prismatic` translates.

*Common mistake:* choosing `revolute`. Without a `<limit>` element, `check_urdf` rejects it with `Joint [...] is of type REVOLUTE but it does not specify limits`.

#### Question 2 — B (2)

Each revolute joint adds one DoF to a serial chain. Grübler's formula for planar mechanisms gives the same result: M = 3(n − 1) − 2·j1 = 3·(3 − 1) − 2·2 = **2**, with n = 3 bodies (ground + 2 moving links) and j1 = 2 one-DoF joints.

*Common mistake:* counting links (3) instead of joints. Read carefully: the question has 3 links **including** the fixed one.

#### Question 3 — A

REP 103: x forward, y left, z up, right-handed. RViz2 draws x red, y green, z blue. Option B is the aerospace NED-style body convention, not ROS.

#### Question 4 — visual → c, collision → b, inertial → a

* `<visual>` is only drawn (RViz2, Gazebo GUI).
* `<collision>` is what the physics engine uses for contact. It is usually simpler than the visual.
* `<inertial>` (mass, centre of mass via `origin`, inertia tensor) determines how forces produce motion.

Award 1 point for all three correct, 0.5 for two.

#### Question 5 — I_zz = 0.037933 kg·m²

```text
I_zz = m (x² + y²) / 12
     = 2.0 × (0.40² + 0.26²) / 12
     = 2.0 × (0.16 + 0.0676) / 12
     = 2.0 × 0.2276 / 12
     = 0.037933 kg·m²
```

Award 0.5 for the correct formula, 0.5 for the correct value (accept 0.0379 or 0.038).

*Common mistakes:* using z instead of x or y (I_zz is about the z axis, so it depends on the dimensions **perpendicular** to z); forgetting the 1/12.

#### Question 6 — (x, y) = (0.300, 0.500), φ = 0°

```text
x = L1 cos θ1 + L2 cos(θ1 + θ2) = 0.5 cos 90° + 0.3 cos 0° = 0 + 0.3 = 0.300 m
y = L1 sin θ1 + L2 sin(θ1 + θ2) = 0.5 sin 90° + 0.3 sin 0° = 0.5 + 0 = 0.500 m
φ = θ1 + θ2 = 0°
```

Physically: link 1 points straight up (+y), and link 2 points back along +x. This configuration is listed in [kinematics_examples.md](kinematics_examples.md#example-4--forward-kinematics-of-a-2-link-planar-arm) and can be checked with `tf2_echo base_link tool0`.

Award 0.5 for (x, y), 0.25 for φ, and 0.25 for the working.

#### Question 7 — B

When Gazebo converts a URDF to SDF, a link without an `<inertial>` block (or with mass ≤ 0) is not modeled, and its parent joint is ignored. The converter prints:

```text
urdf2sdf: link[left_wheel_link] has no <inertial> block defined. ...
urdf2sdf: parent joint[left_wheel_joint] ignored.
urdf2sdf: link[left_wheel_link] is not modeled in sdf.
```

`check_urdf` and RViz2 do not need mass, so they do not report the problem. That is why `gz sdf -p robot.urdf` belongs in the validation routine. (Observed with Gazebo Harmonic / sdformat 14 during course preparation.)

#### Question 8 — C

`robot_state_publisher` reads `robot_description` (the URDF) and subscribes to `/joint_states`. It computes the forward kinematics and publishes fixed-joint transforms on `/tf_static` (latched) and moving-joint transforms on `/tf`. It also publishes `/robot_description`.

* A describes the Gazebo DiffDrive system (or a real robot's base controller).
* B describes `joint_state_publisher`, which publishes `/joint_states`; it does not use `robot_state_publisher`.
* D describes `ros_gz_sim create` together with the URDF → SDF converter.

#### Question 9 — The tool is not using simulation time

The dynamic transforms (`odom → base_footprint` and the wheels) are stamped with **simulation time** from `/clock`, for example "35 s after the simulation started". `view_frames` uses wall-clock time by default, and its clock does not match these timestamps, so the dynamic transforms are not used. The static transforms from `/tf_static` still appear. Once the tool runs on simulation time, its clock and the timestamps agree.

Corrected command:

```bash
ros2 run tf2_tools view_frames --ros-args -p use_sim_time:=true
```

The same applies to `tf2_echo`, RViz2, and any node that uses simulated data. (Observed during course preparation: without `use_sim_time:=true`, the PDF showed only the static frames.)

Award 0.5 for identifying simulation time vs. wall-clock time, and 0.5 for the corrected command.

#### Question 10 — `/cmd_vel` ROS_TO_GZ; `/scan` GZ_TO_ROS; `/clock` GZ_TO_ROS

* `/cmd_vel` is produced in ROS (teleop, Nav2) and consumed by Gazebo's DiffDrive system → **ROS_TO_GZ**.
* `/scan` is produced by Gazebo's `gpu_lidar` sensor → **GZ_TO_ROS**.
* `/clock` is produced by Gazebo's simulation clock and consumed by ROS nodes with `use_sim_time` → **GZ_TO_ROS**.

`BIDIRECTIONAL` would work for some topics, but it is not correct practice here: data flows one way, and a bidirectional bridge can echo messages back.

Award 1 point for all three correct, 0.5 for two.

### Marking Notes (instructors)

* Total: 10 points. Suggested pass mark: 6/10.
* Questions 5, 6, and 9 need working or explanation. A correct number without working gets at most half marks.
* If many students miss Question 7 or 9, review the lab's Exercise 9 and Section 10.5 with the class. These are the two most common simulation problems.

</details>
