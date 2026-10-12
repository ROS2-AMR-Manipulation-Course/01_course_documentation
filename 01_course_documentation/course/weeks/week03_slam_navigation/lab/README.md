# Week 3 Lab — SLAM & Autonomous Navigation (Planned)

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 3
> **Lab Duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic · SLAM Toolbox · Nav2

**Navigation:** [Week 3 README](../README.md) · [Lecture](../lecture/README.md) · **Lab README**

---

> ⚠️ **Status: not yet developed.** This page describes the **planned** scope of the Week 3 lab. The lab guide (`lab_guide.md`) and the exercises (`exercise.md`) have **not** been written. None of the activities below has been implemented or tested for this course yet.

---

## 1. Planned Lab Overview

Students will make a simulated AMR navigate autonomously:

```text
Start simulation ──▶ Check sensors & TF ──▶ SLAM: build and save a map ──▶ Localize on the map
      ──▶ Configure Nav2 ──▶ Send goals ──▶ Measure and evaluate navigation
```

## 2. Planned Activities

| # | Activity                                                       | Purpose                                               |
| - | -------------------------------------------------------------- | ----------------------------------------------------- |
| 1 | Start the Trailobot simulation and check `/scan`, `/odom`, TF  | Confirm the inputs that SLAM and Nav2 need            |
| 2 | Visualize LiDAR data in RViz2                                  | Understand `LaserScan` and sensor frames              |
| 3 | Run SLAM and build a map; save it                              | Mapping                                               |
| 4 | Localize on the saved map                                      | Understand `map → odom`                               |
| 5 | Configure and launch Nav2                                      | Costmaps, planner, controller                         |
| 6 | Send navigation goals (RViz2 and command line)                 | Autonomous navigation                                 |
| 7 | Evaluate navigation behavior with defined metrics              | Validation in simulation                              |

## 3. Planned Prerequisites

* Week 2 lab completed, including Lab Guide Sections 9–10 (Gazebo, bridge, TF with `use_sim_time`).
* Week 3 lecture attended.
* Week 3 packages installed ([software requirements](../../../software_requirements.md#5-packages-per-week)).

## 4. Dependencies Before Development

| Dependency                                   | Status |
| -------------------------------------------- | ------ |
| Trailobot running in Gazebo Harmonic with TF-correct LiDAR frames | ⚠️ Port needed ([Trailobot platform page, Section 7](../../../robot_platforms/trailobot_amr.md#7-proposed-work-before-week-3-)) |
| Portable simulation world for mapping        | ⚠️ The repository world uses absolute paths |
| Single merged scan or a two-LiDAR SLAM configuration | To be decided |
| Tested SLAM Toolbox and Nav2 parameter files for Jazzy | To be developed |

## 5. Planned Deliverables

A saved map, a Nav2 configuration, a recorded navigation run, and a short evaluation report. The exact submission format and rubric will be published in `exercise.md` together with the lab guide.

## 6. References

* [Nav2 references](../../../../../04_references/nav2.md)
* [Trailobot simulation guide](../../../../../02_robot_simulation/trailobot_amr/README.md)
