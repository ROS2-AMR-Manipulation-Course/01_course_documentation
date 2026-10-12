# Week 4 Lecture — Manipulator Kinematics & Motion Planning

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 4
> **Lecture:** 4 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · MoveIt 2

**Navigation:** [Week 4 README](../README.md) · **Lecture README** · [Lab (planned)](../lab/README.md)

---

## 1. Lecture Topic

How does an industrial robot arm move its tool to a desired pose? This lecture builds on the Week 2 forward kinematics. It covers the structure of 6-DoF manipulators, solves the inverse problem (which joint angles give a tool pose?), explains the Jacobian and singularities, and shows how MoveIt 2 plans collision-free trajectories.

## 2. Learning Objectives

After the lecture, students should be able to:

1. Describe the structure of a 6-DoF industrial manipulator and its frames (base, links, flange, tool).
2. Compute forward kinematics from a URDF joint chain.
3. Explain inverse kinematics, why it can have several or no solutions, and how joint limits restrict it.
4. Explain the Jacobian and recognize singular configurations.
5. Distinguish joint-space and Cartesian motion, and describe trajectory generation.
6. Describe the MoveIt 2 workflow: robot model, planning scene, planners, collision checking, trajectory execution.

## 3. Lecture Outline (4 hours)

| Part | Topic                                                          |
| ---- | -------------------------------------------------------------- |
| 1    | Manipulator structure: joints, DoF, workspace, tool frames     |
| 2    | Forward kinematics of a 6-DoF arm (worked on the IRB 1200 joint chain) |
| 3    | Inverse kinematics: analytic vs. numerical, multiple solutions |
| 4    | Jacobian, singularities, velocity kinematics                   |
| 5    | Joint-space vs. Cartesian motion; trajectories                 |
| 6    | MoveIt 2: planning scene, planners, collision checking, execution |

## 4. Slides and Notes

Lecture slides (`slides.pdf`) **will be added by the course maintainer when they are ready.** They will be the visual summary used in class. Detailed lecture notes for this week have not been written yet.

A verified worked example is already available for Part 2. At the zero joint configuration, the IRB 1200 description places `tool0` at (0.533, 0, 0.889) m relative to `base_link`, which is the sum of the joint origins ([IRB 1200 platform page](../../../robot_platforms/abb_irb1200_manipulator.md#3-kinematic-structure--from-irb1200_macrourdfxacro)).

Preparation reading:

* [MoveIt 2 kinematics concepts](https://moveit.picknik.ai/main/doc/concepts/kinematics.html)
* [MoveIt 2 motion planning concepts](https://moveit.picknik.ai/main/doc/concepts/motion_planning.html)
* [Week 2 kinematics examples](../../week02_kinematics_simulation/lecture/kinematics_examples.md) (planar FK)

## 5. Robot-Specific vs. General Content

General manipulator theory (Parts 1, 3–6) applies to any serial arm. Robot-specific data, such as dimensions, limits, packages and launch commands, is kept on the platform pages: [ABB IRB 1200](../../../robot_platforms/abb_irb1200_manipulator.md) and [RB3-1200](../../../robot_platforms/rb3_1200_manipulator.md).
