# ROS 2 AMR & Manipulation — Course Documentation

> **Course:** ROS 2 AMR & Manipulation
> **Format:** 4 weeks · 4 hours lecture + 8 hours laboratory per week (48 contact hours)
> **Environment:** Ubuntu 24.04 LTS · ROS 2 Jazzy · Gazebo Harmonic · RViz2 · Nav2 · MoveIt 2

**Navigation:** [Repository README](../../README.md) · **Course README** · [Syllabus](course_syllabus.md) · [Learning Outcomes](learning_outcomes.md) · [Software](software_requirements.md) · [Assessment](assessment_policy.md) · [Robot Platforms](robot_platforms/README.md)

---

## 1. Course Description

This course introduces the concepts and practical workflows used to develop robotic systems with ROS 2. It focuses on two robot types:

* **Autonomous Mobile Robots (AMRs)** — modeled, simulated, mapped, and navigated.
* **Industrial robot manipulators** — modeled, analyzed kinematically, and motion-planned.

Students learn to model, simulate, inspect, and control robots with the same open-source tools used in industry and research. Theory is always followed by hands-on laboratory work, so every concept is tested on a running system.

---

## 2. How This Documentation Is Organized

```text
01_course_documentation/course/
├── README.md                      ← you are here
├── course_syllabus.md             Weekly topics, schedule, prerequisites
├── learning_outcomes.md           Measurable outcomes per week
├── software_requirements.md       Environment, installation, verification
├── assessment_policy.md           Weights, grading scale, submission and integrity rules
├── robot_platforms/               Trailobot, RB3-1200, ABB IRB 1200
└── weeks/
    ├── week01_ros2_architecture/
    ├── week02_kinematics_simulation/
    ├── week03_slam_navigation/
    └── week04_manipulator_motion_planning/
```

Each week contains:

| Folder     | Contents                                                                                             |
| ---------- | ---------------------------------------------------------------------------------------------------- |
| `README.md` | Week overview: objectives, schedule, prerequisites, deliverables, references                       |
| `lecture/` | `README.md` describing the lecture, plus lecture notes. **Slides (`slides.pdf`) are added by the course maintainer.** |
| `lab/`     | `README.md` (lab overview); where the lab is developed, `lab_guide.md` (step-by-step) and `exercise.md` (tasks, evidence, rubric) |

Related top-level folders:

| Folder                                                           | Purpose                                                         |
| ---------------------------------------------------------------- | --------------------------------------------------------------- |
| [`02_robot_simulation/`](../../02_robot_simulation/README.md)    | How to obtain and run the simulation of each course robot       |
| [`03_student_projects/`](../../03_student_projects/README.md)    | Final project requirements, submission guide, rubric            |
| [`04_references/`](../../04_references/README.md)                | Verified links to official ROS 2, Gazebo, Nav2, MoveIt 2 documentation |

---

## 3. Four-Week Roadmap

| Week | Topic                                    | Main platform                      | Lab status              |
| ---- | ---------------------------------------- | ---------------------------------- | ----------------------- |
| 1    | [Linux, Middleware & ROS 2 Architecture](weeks/week01_ros2_architecture/README.md) | ROS 2, Turtlesim | Available |
| 2    | [Kinematic Modeling & Physics Simulation](weeks/week02_kinematics_simulation/README.md) | Teaching robot `diffbot`; Trailobot model | Available |
| 3    | [SLAM & Autonomous Navigation](weeks/week03_slam_navigation/README.md) | Trailobot (simulation) | Planned — lab not yet developed |
| 4    | [Manipulator Kinematics & Motion Planning](weeks/week04_manipulator_motion_planning/README.md) | ABB IRB 1200, RB3-1200 | Planned — lab not yet developed |

---

## 4. Robot Platforms

| Platform                                                          | Type                              | Used in        |
| ----------------------------------------------------------------- | --------------------------------- | -------------- |
| [Trailobot](robot_platforms/trailobot_amr.md)                     | Differential-drive AMR            | Weeks 1–3      |
| [ABB IRB 1200](robot_platforms/abb_irb1200_manipulator.md)        | 6-DoF industrial manipulator      | Weeks 2 and 4  |
| [RB3-1200](robot_platforms/rb3_1200_manipulator.md)               | 6-DoF manipulator (Rainbow Robotics RB series) | Week 4 |

---

## 5. Learning Approach

The course follows a progressive robotics development workflow:

```text
Robotics concept
       ↓
ROS 2 architecture            (Week 1)
       ↓
Robot modeling                (Week 2)
       ↓
Simulation and sensors        (Week 2)
       ↓
Perception and navigation     (Week 3)
       ↓
Manipulation and planning     (Week 4)
       ↓
Integrated robot project      (03_student_projects)
```

Students first learn the concept, then **apply** it in a guided lab, then **verify and explain** it in exercises. Every lab asks students to predict a result, run the system, and compare.

---

## 6. Main Learning Areas

| Area                    | Topics                                                                                   |
| ----------------------- | ---------------------------------------------------------------------------------------- |
| ROS 2                   | Nodes, topics, services, actions, parameters, launch, ROS graph, CLI, distributed communication |
| Robot modeling          | Links, joints, DoF, coordinate frames, transformations, URDF, Xacro, TF2                 |
| Simulation              | Gazebo Harmonic, physics, collision and inertial properties, sensors, controllers        |
| Mobile robot navigation | LiDAR, LaserScan, occupancy grids, SLAM, localization, costmaps, planning, control, Nav2 |
| Manipulator robotics    | Forward and inverse kinematics, Jacobian, singularity, trajectories, collision checking, MoveIt 2 |

Detailed, measurable outcomes are in [learning_outcomes.md](learning_outcomes.md).

---

## 7. Course Goal

The goal is a practical foundation for developing ROS 2-based robotic systems. By the end of the course, students should understand how robot hardware descriptions, software, simulation, sensors, navigation, and manipulation work together as one system, and be able to build, inspect, and debug such a system themselves.

---

## 8. Where to Start

1. Check the [prerequisites](course_syllabus.md#2-prerequisites) and install the [software](software_requirements.md).
2. Read the [assessment policy](assessment_policy.md).
3. Begin with [Week 1](weeks/week01_ros2_architecture/README.md).
