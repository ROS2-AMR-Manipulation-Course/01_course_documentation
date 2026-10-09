# Week 2 — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Time:** 4 hours lecture · 8 hours laboratory
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** **Week 2 README** · [Lecture Notes](lecture/notes.md) · [Kinematics Examples](lecture/kinematics_examples.md) · [Lab](lab/README.md) · [Assessment](assessment/assessment.md) · [References](resources/reference_links.md)

---

## 1. Overview

In Week 1, students learned how ROS 2 nodes communicate. This week they learn how to **describe a robot** so that software can reason about its shape, motion, and physics. They then run that description in a **physics simulator**.

```text
Robot specification ──▶ Links, joints, frames ──▶ URDF / Xacro ──▶ TF2 ──▶ Gazebo Harmonic
   (what it is)           (kinematic model)        (description)   (frames)   (physics + sensors)
```

By the end of the week, every student has a mobile robot with a LiDAR that they modeled themselves, validated, and drove in Gazebo Harmonic. This prepares them for SLAM and navigation in Week 3.

---

## 2. Learning Objectives

Students should be able to:

* Identify links, joints, and degrees of freedom of a robot.
* Distinguish fixed, revolute, continuous, and prismatic joints.
* Define coordinate frames following ROS conventions (REP 103, REP 105).
* Explain and compose transformations between frames.
* Compute forward kinematics for a planar arm and a differential-drive robot.
* Create a URDF/Xacro robot description with visual, collision, and inertial properties, joint origins, axes, and limits.
* Explain the relationship between URDF, `robot_state_publisher`, and TF2, and inspect a TF tree.
* Configure a robot for Gazebo Harmonic, including physics properties and a basic sensor.
* Run the robot in simulation and verify its behavior.

These match the Week 2 objectives in [course/learning_objectives.md](../../course/learning_objectives.md).

---

## 3. Prerequisites

* Week 1 completed: a working `~/ros2_ws`, sourcing, `colcon build`, topics, and launch files.
* Your `ROS_DOMAIN_ID` set in `~/.bashrc` (Week 1 Lab Guide, Section 2.2).
* Gazebo Harmonic installed and working (`gz sim --versions` prints 8.x). See [course/environment_setup.md](../../course/environment_setup.md).
* Basic trigonometry (sin, cos) and matrix multiplication.

---

## 4. Weekly Plan

### Lecture (4 hours)

| Part | Topic                                                    | Material                                                        |
| ---- | -------------------------------------------------------- | --------------------------------------------------------------- |
| 1    | Robot specification, links, joints, DoF                  | [Notes Part 1](lecture/notes.md#part-1--describing-a-robot)      |
| 2    | Frames, transformations, forward kinematics              | [Notes Part 2](lecture/notes.md#part-2--frames-transformations-and-kinematics), [Examples 1–5](lecture/kinematics_examples.md) |
| 3    | URDF and Xacro                                           | [Notes Part 3](lecture/notes.md#part-3--urdf-and-xacro)          |
| 4    | TF2                                                      | [Notes Part 4](lecture/notes.md#part-4--tf2)                     |
| 5    | Gazebo Harmonic: physics, collision, inertia, sensors    | [Notes Part 5](lecture/notes.md#part-5--physics-simulation-with-gazebo-harmonic), [Example 6](lecture/kinematics_examples.md#example-6--inertia-of-the-lab-robot) |

### Laboratory (8 hours)

| Block  | Activity                                                       | Material                                   |
| ------ | -------------------------------------------------------------- | ------------------------------------------ |
| 1 (3.5 h) | URDF → Xacro → inertia → LiDAR; validation; TF2 and FK      | [Lab Guide 3–8](lab/lab_guide.md), [Exercises 1–6](lab/exercises.md) |
| 2 (2.5 h) | Gazebo Harmonic: world, bridge, launch, driving, physics     | [Lab Guide 9–11](lab/lab_guide.md), [Exercises 7–10](lab/exercises.md) |
| 3 (2 h)   | Debugging challenge, report, practical demonstration         | [Exercise 11](lab/exercises.md), [Practical Assessment](assessment/practical_assessment.md) |

The timed schedule is in [exercises.md](lab/exercises.md#suggested-8-hour-schedule).

---

## 5. Contents

```text
week02_kinematics_simulation/
├── README.md                          # This file
├── lecture/
│   ├── notes.md                       # Lecture notes
│   └── kinematics_examples.md         # Worked examples and practice problems
├── lab/
│   ├── README.md                      # Lab overview and requirements
│   ├── lab_guide.md                   # Step-by-step lab with all package files
│   └── exercises.md                   # Exercises, expected outcomes, rubric, submission
├── assessment/
│   ├── assessment.md                  # How Week 2 is assessed
│   ├── quiz.md                        # 10-question quiz (student version)
│   ├── quiz_answer_key.md             # Answers and explanations (instructor)
│   └── practical_assessment.md        # Practical demonstration and rubric
└── resources/
    └── reference_links.md             # Official ROS 2 Jazzy and Gazebo Harmonic documentation
```

---

## 6. Deliverables and Assessment

| Item                         | Where                                                          | Course component (see [course/assessment.md](../../course/assessment.md)) |
| ---------------------------- | -------------------------------------------------------------- | ----------------------------------------- |
| Lab package + report         | [exercises.md — Submission](lab/exercises.md#submission)       | Weekly Laboratory Work                    |
| Quiz (10 questions)          | [assessment/quiz.md](assessment/quiz.md)                       | Weekly Exercises / Assignments            |
| Practical demonstration      | [assessment/practical_assessment.md](assessment/practical_assessment.md) | Practical Demonstrations        |

Details: [assessment/assessment.md](assessment/assessment.md).

---

## 7. Notes for Instructors

* **The lab robot vs. Trailobot:** the course schedule names Trailobot as the Week 2 platform. This documentation teaches the modeling skills on `diffbot`, a small robot with the same structure, which students build from scratch. If the Trailobot repository is ready, use [Exercise 12](lab/exercises.md#exercise-12-optional--compare-with-the-trailobot-model) or replace Block 3 with running and inspecting the Trailobot simulation.
* **Testing status:** the lab package files were built and run with ROS 2 Jazzy and Gazebo Harmonic (gz-sim 8.10) from the RoboStack conda distribution, on Ubuntu 24.04 with a virtual display and software rendering. They have **not** yet been tested with the apt packages on the lab computers. Do one full run-through on a lab machine before the first session.
* **Answer key:** `assessment/quiz_answer_key.md` is in the same repository as the student material. If the repository is public to students, move the answer key to a private instructor repository before publishing.
* **Virtual machines:** Gazebo needs OpenGL. Check the lab computers or VMs with `gz sim shapes.sdf` before class.

---

## 8. Next Week

**Week 3 — SLAM & Autonomous Navigation:** using the simulated mobile robot's LiDAR, odometry, and TF tree to build maps with SLAM Toolbox and navigate with Nav2.
