# Week 2 Quiz — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Time:** 25 minutes
> **Questions:** 10 (1 point each)
> **Allowed:** A calculator. No notes.

**Navigation:** [Week 2 README](../README.md) · [Assessment Overview](assessment.md) · **Quiz** · [Practical Assessment](practical_assessment.md)

---

**Instructions:** For multiple-choice questions, choose **one** answer. For short-answer questions, show your working. Give numerical answers to 3 decimal places. All angles in ROS are in radians unless stated otherwise.

---

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

---

*End of quiz. Answers are provided to instructors separately.*
