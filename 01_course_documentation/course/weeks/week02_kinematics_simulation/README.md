# Week 2 — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Time:** 4 hours lecture · 8 hours laboratory
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic · RViz2

**Navigation:** [Course README](../../README.md) · **Week 2 README** · [Lecture](lecture/README.md) · [Lab](lab/README.md) · [Previous: Week 1](../week01_ros2_architecture/README.md) · [Next: Week 3](../week03_slam_navigation/README.md)

---

## 1. Overview

In Week 1, students learned how ROS 2 nodes communicate. This week they learn how to **describe a robot** so that software can reason about its shape, motion, and physics. Then they run that description in a **physics simulator**.

```text
Robot specification ──▶ Links, joints, frames ──▶ URDF / Xacro ──▶ TF2 ──▶ Gazebo Harmonic
   (what it is)           (kinematic model)        (description)   (frames)   (physics + sensors)
```

By the end of the week, every student has a mobile robot with a LiDAR that they modeled, validated, and drove in Gazebo Harmonic themselves. This prepares them for SLAM and navigation with Trailobot in Week 3.

---

## 2. Learning Objectives

Students should be able to:

* Identify links, joints, and degrees of freedom of a robot.
* Distinguish fixed, revolute, continuous, and prismatic joints.
* Define coordinate frames following ROS conventions (REP 103, REP 105).
* Explain and compose transformations between frames.
* Compute forward kinematics for a planar arm and a differential-drive robot.
* Create a URDF/Xacro description with visual, collision, and inertial properties, joint origins, axes, and limits.
* Explain the relationship between URDF, `robot_state_publisher`, and TF2, and visualize the result in RViz2.
* Configure a robot for Gazebo Harmonic, including physics properties and a basic sensor.
* Run the robot in simulation and verify its behavior.

Measurable versions: [learning outcomes, Week 2](../../learning_outcomes.md#week-2--kinematic-modeling--physics-simulation).

---

## 3. Prerequisites

* Week 1 completed: a working `~/ros2_ws`, sourcing, `colcon build`, topics, and launch files.
* `ROS_DOMAIN_ID` set in `~/.bashrc` ([Week 1 Lab Guide, Section 2.2](../week01_ros2_architecture/lab/lab_guide.md)).
* Gazebo Harmonic installed and working: `gz sim --versions` prints 8.x ([software requirements](../../software_requirements.md)).
* Basic trigonometry (sin, cos) and matrix multiplication.

---

## 4. Weekly Plan

### Lecture (4 hours) → [lecture/README.md](lecture/README.md)

| Part | Topic                                                 |
| ---- | ----------------------------------------------------- |
| 1    | Robot specification, links, joints, DoF               |
| 2    | Frames, transformations, forward kinematics           |
| 3    | URDF and Xacro                                        |
| 4    | TF2 and RViz2                                         |
| 5    | Gazebo Harmonic: physics, collision, inertia, sensors |

### Laboratory (8 hours) → [lab/README.md](lab/README.md)

| Block     | Activity                                                       | Material                                                         |
| --------- | -------------------------------------------------------------- | ---------------------------------------------------------------- |
| 1 (3.5 h) | URDF → Xacro → inertia → LiDAR; validation; TF2 and FK         | [Lab Guide 3–8](lab/lab_guide.md), [Exercises 1–6](lab/exercise.md) |
| 2 (2.5 h) | Gazebo Harmonic: world, bridge, launch, driving, physics       | [Lab Guide 9–11](lab/lab_guide.md), [Exercises 7–10](lab/exercise.md) |
| 3 (2 h)   | Debugging challenge, report, practical demonstration           | [Exercise 11](lab/exercise.md), [Practical Demonstration](lab/exercise.md#practical-demonstration) |

Timed schedule: [exercise.md — Suggested 8-Hour Schedule](lab/exercise.md#suggested-8-hour-schedule).

---

## 5. Contents

```text
week02_kinematics_simulation/
├── README.md                     This file
├── lecture/
│   ├── README.md                 Lecture overview, slides, review quiz with answers
│   ├── notes.md                  Lecture notes
│   ├── kinematics_examples.md    Worked examples and practice problems
│   └── Week2 -Kinematic_Modeling_&_Physics_Simulation.pdf   Slides (course maintainer)
└── lab/
    ├── README.md                 Lab overview, requirements, workflow
    ├── lab_guide.md              Step-by-step lab with every package file and troubleshooting
    └── exercise.md               Exercises, evidence, submission, rubric, practical demonstration
```

---

## 6. Deliverables and Assessment

| Item                         | Where                                                                    | Course component ([assessment policy](../../assessment_policy.md)) |
| ---------------------------- | ------------------------------------------------------------------------ | ---------------------------------- |
| Lab package + report         | [exercise.md — Submission](lab/exercise.md#submission)                   | Weekly laboratory work             |
| Review quiz (10 questions)   | [lecture/README.md — Review Quiz](lecture/README.md#6-review-quiz)       | Weekly exercises / quizzes         |
| Practical demonstration      | [exercise.md — Practical Demonstration](lab/exercise.md#practical-demonstration) | Practical demonstrations     |

---

## 7. References

Key official documentation for this week (all verified; full lists in [04_references](../../../../04_references/README.md)):

| Topic                                     | Link |
| ----------------------------------------- | ---- |
| URDF tutorials (ROS 2 Jazzy)              | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/URDF-Main.html |
| Adding physical and collision properties  | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Adding-Physical-and-Collision-Properties-to-a-URDF-Model.html |
| Using Xacro to clean up a URDF            | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Using-Xacro-to-Clean-Up-a-URDF-File.html |
| TF2 tutorials                             | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Tf2-Main.html |
| URDF XML specification                    | https://wiki.ros.org/urdf/XML |
| REP 103 / REP 105                         | https://reps.openrobotics.org/rep-0103/ · https://reps.openrobotics.org/rep-0105/ |
| Gazebo Harmonic: ROS installation pairing | https://gazebosim.org/docs/harmonic/ros_installation |
| Launch Gazebo from ROS 2                  | https://gazebosim.org/docs/harmonic/ros2_launch_gazebo |
| ROS 2 ↔ Gazebo bridge                     | https://gazebosim.org/docs/harmonic/ros2_integration |
| Gazebo sensors                            | https://gazebosim.org/docs/harmonic/sensors |

By topic: [URDF/Xacro/TF2](../../../../04_references/urdf_xacro_tf2.md) · [Gazebo Harmonic](../../../../04_references/gazebo_harmonic.md) · [ROS 2 Jazzy](../../../../04_references/ros2_jazzy.md)

---

## 8. Notes for Instructors

* **Lab robot vs. Trailobot:** the lab teaches modeling on `diffbot`, a small robot with Trailobot's structure that students build from scratch. Exercise 12 compares it with the real [Trailobot description](../../robot_platforms/trailobot_amr.md). Trailobot's model validates on Jazzy, but its Gazebo configuration targets Fortress, so students do not drive Trailobot in Gazebo this week.
* **Testing status:** the lab package files were built and run with ROS 2 Jazzy and Gazebo Harmonic (gz-sim 8.10) from the RoboStack conda distribution, on Ubuntu 24.04 with a virtual display and software rendering. They have **not** yet been tested with the apt packages on the lab computers. Do one full run-through on a lab machine before the first session.
* **Quiz answers** are in a collapsed section of the [lecture README](lecture/README.md#6-review-quiz). If you use the quiz for grading, give it before students open that page, or keep a private copy.
* **Virtual machines:** check Gazebo with `gz sim shapes.sdf` before class; see the [lab guide troubleshooting](lab/lab_guide.md#116-gazebo-spawn-failures).

---

## 9. Next Week

**Week 3 — SLAM & Autonomous Navigation:** using a simulated mobile robot's LiDAR, odometry, and TF tree to build maps with SLAM Toolbox and navigate with Nav2. → [Week 3](../week03_slam_navigation/README.md)
