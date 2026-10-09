# Week 2 Quiz — Answer Key (Instructor Only)

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **For:** [quiz.md](quiz.md)

> **Do not distribute to students.** This file is kept separate from the student quiz. If this repository is visible to students, move this file to a private instructor repository before the quiz.

**Navigation:** [Assessment Overview](assessment.md) · [Quiz](quiz.md) · **Answer Key**

---

## Summary

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

---

## Answers and Explanations

### Question 1 — C (`continuous`)

A `continuous` joint rotates about its axis without position limits, which is what a wheel needs. `revolute` also rotates but **requires** `lower`/`upper` limits. `fixed` does not move. `prismatic` translates.

*Common mistake:* choosing `revolute`. Without a `<limit>` element, `check_urdf` rejects it with `Joint [...] is of type REVOLUTE but it does not specify limits`.

### Question 2 — B (2)

Each revolute joint adds one DoF to a serial chain. Grübler's formula for planar mechanisms gives the same result: M = 3(n − 1) − 2·j1 = 3·(3 − 1) − 2·2 = **2**, with n = 3 bodies (ground + 2 moving links) and j1 = 2 one-DoF joints.

*Common mistake:* counting links (3) instead of joints. Read carefully: the question has 3 links **including** the fixed one.

### Question 3 — A

REP 103: x forward, y left, z up, right-handed. RViz2 draws x red, y green, z blue. Option B is the aerospace NED-style body convention, not ROS.

### Question 4 — visual → c, collision → b, inertial → a

* `<visual>` is only drawn (RViz2, Gazebo GUI).
* `<collision>` is what the physics engine uses for contact. It is usually simpler than the visual.
* `<inertial>` (mass, centre of mass via `origin`, inertia tensor) determines how forces produce motion.

Award 1 point for all three correct, 0.5 for two.

### Question 5 — I_zz = 0.037933 kg·m²

```text
I_zz = m (x² + y²) / 12
     = 2.0 × (0.40² + 0.26²) / 12
     = 2.0 × (0.16 + 0.0676) / 12
     = 2.0 × 0.2276 / 12
     = 0.037933 kg·m²
```

Award 0.5 for the correct formula, 0.5 for the correct value (accept 0.0379 or 0.038).

*Common mistakes:* using z instead of x or y (I_zz is about the z axis, so it depends on the dimensions **perpendicular** to z); forgetting the 1/12.

### Question 6 — (x, y) = (0.300, 0.500), φ = 0°

```text
x = L1 cos θ1 + L2 cos(θ1 + θ2) = 0.5 cos 90° + 0.3 cos 0° = 0 + 0.3 = 0.300 m
y = L1 sin θ1 + L2 sin(θ1 + θ2) = 0.5 sin 90° + 0.3 sin 0° = 0.5 + 0 = 0.500 m
φ = θ1 + θ2 = 0°
```

Physically: link 1 points straight up (+y), and link 2 points back along +x. This configuration is listed in [kinematics_examples.md](../lecture/kinematics_examples.md#example-4--forward-kinematics-of-a-2-link-planar-arm) and can be checked with `tf2_echo base_link tool0`.

Award 0.5 for (x, y), 0.25 for φ, and 0.25 for the working.

### Question 7 — B

When Gazebo converts a URDF to SDF, a link without an `<inertial>` block (or with mass ≤ 0) is not modeled, and its parent joint is ignored. The converter prints:

```text
urdf2sdf: link[left_wheel_link] has no <inertial> block defined. ...
urdf2sdf: parent joint[left_wheel_joint] ignored.
urdf2sdf: link[left_wheel_link] is not modeled in sdf.
```

`check_urdf` and RViz2 do not need mass, so they do not report the problem. That is why `gz sdf -p robot.urdf` belongs in the validation routine. (Observed with Gazebo Harmonic / sdformat 14 during course preparation.)

### Question 8 — C

`robot_state_publisher` reads `robot_description` (the URDF) and subscribes to `/joint_states`. It computes the forward kinematics and publishes fixed-joint transforms on `/tf_static` (latched) and moving-joint transforms on `/tf`. It also publishes `/robot_description`.

* A describes the Gazebo DiffDrive system (or a real robot's base controller).
* B describes `joint_state_publisher`, which publishes `/joint_states`; it does not use `robot_state_publisher`.
* D describes `ros_gz_sim create` together with the URDF → SDF converter.

### Question 9 — The tool is not using simulation time

The dynamic transforms (`odom → base_footprint` and the wheels) are stamped with **simulation time** from `/clock`, for example "35 s after the simulation started". `view_frames` uses wall-clock time by default, and its clock does not match these timestamps, so the dynamic transforms are not used. The static transforms from `/tf_static` still appear. Once the tool runs on simulation time, its clock and the timestamps agree.

Corrected command:

```bash
ros2 run tf2_tools view_frames --ros-args -p use_sim_time:=true
```

The same applies to `tf2_echo`, RViz2, and any node that uses simulated data. (Observed during course preparation: without `use_sim_time:=true`, the PDF showed only the static frames.)

Award 0.5 for identifying simulation time vs. wall-clock time, and 0.5 for the corrected command.

### Question 10 — `/cmd_vel` ROS_TO_GZ; `/scan` GZ_TO_ROS; `/clock` GZ_TO_ROS

* `/cmd_vel` is produced in ROS (teleop, Nav2) and consumed by Gazebo's DiffDrive system → **ROS_TO_GZ**.
* `/scan` is produced by Gazebo's `gpu_lidar` sensor → **GZ_TO_ROS**.
* `/clock` is produced by Gazebo's simulation clock and consumed by ROS nodes with `use_sim_time` → **GZ_TO_ROS**.

`BIDIRECTIONAL` would work for some topics, but it is not correct practice here: data flows one way, and a bidirectional bridge can echo messages back.

Award 1 point for all three correct, 0.5 for two.

---

## Grading Notes

* Total: 10 points. Suggested pass mark: 6/10.
* Questions 5, 6, and 9 need working or explanation. A correct number without working gets at most half marks.
* If many students miss Question 7 or 9, review the lab's Exercise 9 and Section 10.5 with the class. These are the two most common simulation problems.
