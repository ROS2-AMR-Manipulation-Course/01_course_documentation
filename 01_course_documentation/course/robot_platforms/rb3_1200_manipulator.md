# RB3-1200 — Collaborative Manipulator

**Navigation:** [Robot Platforms](README.md) · [Trailobot](trailobot_amr.md) · [ABB IRB 1200](abb_irb1200_manipulator.md) · **RB3-1200** · [Simulation guide](../../../02_robot_simulation/rb3_1200/README.md)

> **Status:** ⚠️ **Robot-specific details are not yet documented.** No RB3-1200 robot description, ROS 2 package, or project files were available when this page was written. This page contains only verified information and lists what is still needed.

---

## 1. What Is Known

* The RB3-1200 belongs to Rainbow Robotics' **RB series** of collaborative robots.
* The course documentation describes it as a **6-DoF** manipulator used in Week 4.
* Rainbow Robotics publishes **rbpodo**, an official client library for RB-series cobots (C++17 and Python, Apache-2.0): [github.com/RainbowRobotics/rbpodo](https://github.com/RainbowRobotics/rbpodo). It is a robot-controller client API, **not** a ROS 2 package, and it contains no URDF. Its README states that the API is under development and may change. The library does not name the RB3-1200 specifically; check compatibility with your controller version.

No public ROS 2 description or simulation package for the RB3-1200 was found under the repository names checked (October 2026).

## 2. Educational Purpose

The RB3-1200 is the course's second manipulator platform. It complements the ABB IRB 1200:

| Topic (Week 4)                | Why a second robot helps                                                                 |
| ----------------------------- | ---------------------------------------------------------------------------------------- |
| Kinematic structure           | Students compare joint layouts, frames, and limits of two 6-DoF arms                    |
| Robot description reuse       | The same URDF/TF/MoveIt workflow applied to a different robot shows what is generic and what is robot-specific |
| Collaborative vs. industrial  | Discussion of safety concepts and workspace sharing (theory; no hardware lab planned yet) |

Week 2 concepts used: links, joints, joint origins/axes/limits, TF2, forward kinematics.

## 3. Information Still Needed (do not invent)

Before an RB3-1200 lab can be written, the course maintainer needs to supply or confirm:

| Item                                            | Source to use                                   |
| ----------------------------------------------- | ----------------------------------------------- |
| Kinematic dimensions (link lengths, offsets)    | Manufacturer datasheet or an official robot description |
| Joint limits (position, velocity, effort)       | Manufacturer datasheet                          |
| URDF/Xacro description and meshes               | Manufacturer or a maintained ROS package, with its license |
| ROS 2 package names and launch files            | The chosen description/driver repository        |
| MoveIt 2 configuration (SRDF, kinematics, controllers) | Existing config, or generated with the MoveIt Setup Assistant |
| Control interface used in the lab (simulation only, `rbpodo`, or a ROS 2 driver) | Course decision |

When these are available, document them here and in the [simulation guide](../../../02_robot_simulation/rb3_1200/README.md), keeping hardware-specific details separate from the general manipulator theory in the Week 4 lecture.

## 4. Related Documents

* [ABB IRB 1200](abb_irb1200_manipulator.md) — the documented manipulator platform
* [Week 4 — Manipulator Kinematics & Motion Planning](../weeks/week04_manipulator_motion_planning/README.md)
* [MoveIt 2 references](../../../04_references/moveit2.md)
