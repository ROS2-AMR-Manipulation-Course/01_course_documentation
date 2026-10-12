# Week 4 Lab — Manipulator Kinematics & Motion Planning (Planned)

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 4
> **Lab Duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic · RViz2 · MoveIt 2

**Navigation:** [Week 4 README](../README.md) · [Lecture](../lecture/README.md) · **Lab README**

---

> ⚠️ **Status: not yet developed.** This page describes the **planned** scope of the Week 4 lab. The lab guide (`lab_guide.md`) and the exercises (`exercise.md`) have **not** been written. None of the activities below has been implemented or tested for this course yet.

---

## 1. Planned Lab Overview

Students will plan and validate motions of a simulated industrial manipulator:

```text
Load robot description ──▶ Inspect joints & tool frame ──▶ Verify FK ──▶ Plan with MoveIt 2
      ──▶ Joint-space vs. Cartesian goals ──▶ Add collision objects ──▶ Validate trajectories
```

## 2. Planned Activities

| # | Activity                                                              | Purpose                                         |
| - | --------------------------------------------------------------------- | ----------------------------------------------- |
| 1 | Generate and validate the IRB 1200 URDF; inspect joints, limits, frames | Connect Week 2 skills to a real arm           |
| 2 | Verify forward kinematics at chosen joint configurations with TF2     | FK by hand vs. `tf2_echo`                       |
| 3 | Launch the robot with MoveIt 2 in simulation                          | MoveIt 2 workflow                               |
| 4 | Plan to joint-space and Cartesian (pose) goals in RViz2               | Compare motion types; observe IK solutions      |
| 5 | Add collision objects to the planning scene and re-plan               | Collision checking                              |
| 6 | Approach a singular configuration and observe the planner             | Singularities in practice                       |
| 7 | Inspect and evaluate trajectories (time, joint limits, smoothness)    | Trajectory validation                           |

## 3. Planned Prerequisites

* Week 2 lab completed (URDF, TF2, forward kinematics).
* Week 4 lecture attended.
* MoveIt 2 and the robot packages installed ([software requirements](../../../software_requirements.md#5-packages-per-week), [IRB 1200 simulation guide](../../../../../02_robot_simulation/abb_irb1200/README.md)).

## 4. Dependencies Before Development

| Dependency                                   | Status |
| -------------------------------------------- | ------ |
| IRB 1200 description (IFRA-Cranfield, `jazzy`) | ✅ Validated on Jazzy (URDF, zero-pose FK) |
| IRB 1200 Gazebo + MoveIt 2 launch (`ros2srrc_launch`) | Not yet tested by the course maintainers |
| RB3-1200 description and MoveIt configuration | ⚠️ Not yet available ([details](../../../robot_platforms/rb3_1200_manipulator.md)) |

## 5. Planned Deliverables

Planned trajectories with collision objects, an FK verification table, and a short evaluation report. The exact submission format and rubric will be published in `exercise.md` together with the lab guide.

## 6. References

* [MoveIt 2 references](../../../../../04_references/moveit2.md)
* [ABB IRB 1200 simulation guide](../../../../../02_robot_simulation/abb_irb1200/README.md)
