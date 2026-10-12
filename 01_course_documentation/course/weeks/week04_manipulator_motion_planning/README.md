# Week 4 — Manipulator Kinematics & Motion Planning

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 4
> **Time:** 4 hours lecture · 8 hours laboratory
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic · RViz2 · MoveIt 2
> **Status:** Lecture outline available · **Lab not yet developed**

**Navigation:** [Course README](../../README.md) · **Week 4 README** · [Lecture](lecture/README.md) · [Lab (planned)](lab/README.md) · [Previous: Week 3](../week03_slam_navigation/README.md)

---

## 1. Overview

Week 4 moves from mobile robots to **industrial manipulators**. Students apply the frames, joints, and forward kinematics from Week 2 to 6-DoF arms. They then learn how inverse kinematics and motion planning let a robot reach a target pose without collisions.

```text
Robot description ──▶ Joint states ──▶ Target pose ──▶ Inverse kinematics ──▶ Motion planning ──▶ Collision check ──▶ Trajectory
```

Platforms: [ABB IRB 1200](../../robot_platforms/abb_irb1200_manipulator.md) and [RB3-1200](../../robot_platforms/rb3_1200_manipulator.md).

## 2. Topics

* Six-degree-of-freedom industrial manipulators: structure, joints, frames.
* Joint-space and Cartesian motion.
* Forward and inverse kinematics; multiple IK solutions.
* End-effector and tool frames (`flange`, `tool0`).
* Jacobian and singularities.
* MoveIt 2 configuration and motion planning.
* Collision checking and trajectory validation.

## 3. Learning Objectives

Students should be able to:

* Identify the joints, DoF, and frames of a 6-DoF manipulator.
* Compute forward kinematics and explain inverse kinematics.
* Explain the Jacobian and identify singular configurations.
* Distinguish joint-space and Cartesian motion.
* Plan, check for collisions, and validate trajectories with MoveIt 2 in simulation.

Measurable versions: [learning outcomes, Week 4](../../learning_outcomes.md#week-4--manipulator-kinematics--motion-planning).

## 4. Prerequisites

* Week 2 completed: URDF joint origins, axes, limits; TF2; forward kinematics.
* Week 4 packages installed ([software requirements, Section 5](../../software_requirements.md#5-packages-per-week)).

## 5. Status

| Part      | Status |
| --------- | ------ |
| Lecture   | [Outline](lecture/README.md) available; slides to be uploaded by the course maintainer |
| Lab       | **Not yet developed.** See the [planned scope](lab/README.md). There is no lab guide or exercise file yet. |
| ABB IRB 1200 | Description validated on Jazzy; zero-pose FK checked. Simulation and MoveIt 2 launch not yet tested ([details](../../robot_platforms/abb_irb1200_manipulator.md)) |
| RB3-1200  | Robot description not yet available ([details](../../robot_platforms/rb3_1200_manipulator.md)) |

## 6. References

| Topic                         | Link |
| ----------------------------- | ---- |
| MoveIt 2 getting started      | https://moveit.picknik.ai/main/doc/tutorials/getting_started/getting_started.html |
| MoveIt 2 kinematics concepts  | https://moveit.picknik.ai/main/doc/concepts/kinematics.html |
| MoveIt 2 motion planning concepts | https://moveit.picknik.ai/main/doc/concepts/motion_planning.html |
| MoveIt Setup Assistant        | https://moveit.picknik.ai/main/doc/examples/setup_assistant/setup_assistant_tutorial.html |
| IRB 1200 description source   | https://github.com/IFRA-Cranfield/ros2_SimRealRobotControl (branch `jazzy`) |

More: [04_references/moveit2.md](../../../../04_references/moveit2.md).
