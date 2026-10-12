# Week 3 — SLAM & Autonomous Navigation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 3
> **Time:** 4 hours lecture · 8 hours laboratory
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic · RViz2 · SLAM Toolbox · Nav2
> **Status:** Lecture outline available · **Lab not yet developed**

**Navigation:** [Course README](../../README.md) · **Week 3 README** · [Lecture](lecture/README.md) · [Lab (planned)](lab/README.md) · [Previous: Week 2](../week02_kinematics_simulation/README.md) · [Next: Week 4](../week04_manipulator_motion_planning/README.md)

---

## 1. Overview

In Week 2, students built a robot description and drove a robot in simulation. In Week 3 the robot becomes **autonomous**: it builds a map of an unknown environment, knows where it is on that map, and plans and follows paths to goals while avoiding obstacles.

```text
LiDAR + odometry ──▶ SLAM (map) ──▶ Localization (map → odom) ──▶ Costmaps ──▶ Planning + control (Nav2) ──▶ /cmd_vel
```

The main platform is the **Trailobot** AMR in simulation ([platform page](../../robot_platforms/trailobot_amr.md)).

## 2. Topics

* Mobile robot motion and sensors (wheel odometry, IMU, 2D LiDAR).
* `LaserScan` data and occupancy-grid maps.
* Mapping and SLAM.
* Localization on a known map, and the `map → odom → base_link` frame relationship.
* Global and local costmaps.
* Nav2 architecture: planners, controllers, behavior trees, recovery behaviors.
* Path planning and autonomous navigation.
* Navigation validation in simulation.

## 3. Learning Objectives

Students should be able to:

* Explain how a 2D LiDAR produces `LaserScan` data and how an occupancy grid represents space.
* Describe the SLAM process and generate a map of a simulated environment.
* Explain localization on a known map and the `map → odom → base_link` TF relationship.
* Explain global and local costmaps and the main components of Nav2.
* Send navigation goals and evaluate navigation behavior with measurable criteria.

Measurable versions: [learning outcomes, Week 3](../../learning_outcomes.md#week-3--slam--autonomous-navigation).

## 4. Prerequisites

* Week 2 completed: a robot description with correct frames, a Gazebo Harmonic simulation, `ros_gz_bridge`, and `use_sim_time`.
* Understanding of `odom → base_footprint` and of sensor frames from Week 2.
* Week 3 packages installed ([software requirements, Section 5](../../software_requirements.md#5-packages-per-week)).

## 5. Status

| Part     | Status |
| -------- | ------ |
| Lecture  | [Outline](lecture/README.md) available; slides to be uploaded by the course maintainer |
| Lab      | **Not yet developed.** See the [planned scope](lab/README.md). There is no lab guide or exercise file yet. |
| Platform | Trailobot needs a Gazebo Harmonic port before it can be used for this lab ([details](../../robot_platforms/trailobot_amr.md#7-proposed-work-before-week-3-)) |

## 6. References

| Topic                              | Link |
| ---------------------------------- | ---- |
| Nav2 documentation (Jazzy)         | https://docs.nav2.org/jazzy/ |
| Nav2 navigation concepts           | https://docs.nav2.org/jazzy/getting_started/navigation_concepts/ |
| Nav2 first-time robot setup guide  | https://docs.nav2.org/jazzy/configuration_and_development/first_time_robot_setup_guide/ |
| Navigating while mapping (SLAM)    | https://docs.nav2.org/jazzy/tutorials/general_tutorials/navigation2_with_slam/navigation2_with_slam/ |
| SLAM Toolbox                       | https://github.com/SteveMacenski/slam_toolbox (branch `jazzy`) |
| REP 105 — mobile platform frames   | https://reps.openrobotics.org/rep-0105/ |

More: [04_references/nav2.md](../../../../04_references/nav2.md).
