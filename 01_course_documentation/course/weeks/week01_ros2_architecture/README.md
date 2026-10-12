# Week 1 — Linux, Middleware & ROS 2 Architecture

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Time:** 4 hours lecture · 8 hours laboratory
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Turtlesim

**Navigation:** [Course README](../../README.md) · **Week 1 README** · [Lecture](lecture/README.md) · [Lab](lab/README.md) · [Next: Week 2](../week02_kinematics_simulation/README.md)

---

## 1. Overview

Week 1 builds the software foundation for the rest of the course. Students learn how Linux, middleware, and ROS 2 let many robot programs work together. They then build a small ROS 2 system themselves, which by the end of the lab is a PS4 controller driving a simulated turtle, started from a single launch file.

```text
Linux & processes ──▶ Middleware ──▶ ROS 2 nodes ──▶ Topics / Services / Actions / Parameters ──▶ Launch & debugging
```

## 2. Learning Objectives

Students should be able to:

* Explain the role of Linux and middleware in robotics.
* Describe the ROS 2 architecture: nodes, topics, services, actions, parameters.
* Create, build, and run ROS 2 Python packages in a `colcon` workspace.
* Inspect and debug a running system with the ROS 2 CLI, `rqt_graph`, and `rqt_console`.
* Start a multi-node system with a launch file and a YAML configuration.

Measurable versions: [learning outcomes, Week 1](../../learning_outcomes.md#week-1--linux-middleware--ros-2-architecture).

## 3. Prerequisites

* Ubuntu 24.04 with ROS 2 Jazzy installed and verified ([software requirements](../../software_requirements.md)).
* Basic Linux terminal and Python skills ([syllabus — prerequisites](../../course_syllabus.md#2-prerequisites)).
* A PS4 controller is helpful for the second half of the lab. A software fallback is provided.

## 4. Weekly Plan

| Part        | Content                                                                 | Material |
| ----------- | ----------------------------------------------------------------------- | -------- |
| Lecture (4 h) | Linux, processes, middleware, ROS 2 architecture and communication, CLI, launch | [lecture/README.md](lecture/README.md) |
| Lab (8 h)   | Workspace → publisher/subscriber → services → actions → parameters → PS4 teleoperation → status node → launch file | [lab/README.md](lab/README.md) |

## 5. Contents

```text
week01_ros2_architecture/
├── README.md
├── lecture/
│   ├── README.md                                     Lecture overview
│   ├── notes.md                                      Lecture notes
│   └── Week1-Linux,Middleware&ROS2_Architecture.pdf  Slides (course maintainer)
└── lab/
    ├── README.md                                     Lab overview and requirements
    ├── lab_guide.md                                  Step-by-step lab
    └── exercise.md                                   Exercises, submission, rubric
```

## 6. Deliverables and Assessment

| Item                    | Where                                                      | Course component ([assessment policy](../../assessment_policy.md)) |
| ----------------------- | ---------------------------------------------------------- | ---------------------- |
| Lab packages + report   | [exercise.md — Submission](lab/exercise.md#submission)     | Weekly laboratory work |
| Exercise questions      | [exercise.md](lab/exercise.md)                             | Weekly exercises       |
| Demonstration           | Launch-file demonstration in [exercise.md](lab/exercise.md) | Practical demonstrations |

## 7. References

| Topic                                   | Link |
| --------------------------------------- | ---- |
| ROS 2 Jazzy documentation               | https://docs.ros.org/en/jazzy/ |
| Installation on Ubuntu                  | https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html |
| Domain IDs (classroom network isolation) | https://docs.ros.org/en/jazzy/Concepts/Intermediate/About-Domain-ID.html |

More: [04_references/ros2_jazzy.md](../../../../04_references/ros2_jazzy.md).

## 8. Next Week

**Week 2 — Kinematic Modeling & Physics Simulation:** describing a robot with URDF/Xacro and TF2, and simulating it in Gazebo Harmonic. → [Week 2](../week02_kinematics_simulation/README.md)
