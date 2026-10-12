# Week 3 Lecture — SLAM & Autonomous Navigation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 3
> **Lecture:** 4 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic · SLAM Toolbox · Nav2

**Navigation:** [Week 3 README](../README.md) · **Lecture README** · [Lab (planned)](../lab/README.md)

---

## 1. Lecture Topic

How does a mobile robot move autonomously in an environment it has never seen? This lecture follows the data from the sensors to the motors: LiDAR scans and odometry are combined into a map (SLAM), the robot estimates its pose on that map (localization), the map is turned into costmaps, and Nav2 plans a path and sends velocity commands.

## 2. Learning Objectives

After the lecture, students should be able to:

1. Describe the sensors of a mobile robot (wheel encoders, IMU, 2D LiDAR) and the errors each one has.
2. Explain the `sensor_msgs/msg/LaserScan` message and an occupancy-grid map.
3. Explain the SLAM problem and the role of loop closure.
4. Explain localization on a known map and why `map → odom` is published separately from `odom → base_link`.
5. Describe global and local costmaps and their layers (static, obstacle, inflation).
6. Describe the Nav2 architecture: planner, controller, behavior tree, recovery behaviors.
7. Define measurable criteria for validating navigation in simulation.

## 3. Lecture Outline (4 hours)

| Part | Topic                                                           |
| ---- | --------------------------------------------------------------- |
| 1    | Mobile robot motion and sensors; odometry drift                 |
| 2    | LiDAR, `LaserScan`, occupancy grids                             |
| 3    | SLAM: mapping and localization at the same time                 |
| 4    | Localization on a known map; REP 105 frames (`map`, `odom`, `base_link`) |
| 5    | Costmaps and Nav2: planning, control, behavior trees, recovery  |
| 6    | Validating navigation: success rate, path length, time, safety  |

## 4. Slides and Notes

Lecture slides (`slides.pdf`) **will be added by the course maintainer when they are ready.** They will be the visual summary used in class. Detailed lecture notes for this week have not been written yet.

Until then, prepare with the official documentation:

* [Nav2 navigation concepts (Jazzy)](https://docs.nav2.org/jazzy/getting_started/navigation_concepts/)
* [Nav2 state estimation (frames)](https://docs.nav2.org/jazzy/getting_started/navigation_concepts/state_estimation/)
* [REP 105 — Coordinate frames for mobile platforms](https://reps.openrobotics.org/rep-0105/)

## 5. Connection to Earlier Weeks

| Week 2 concept                               | Used in Week 3 for                         |
| -------------------------------------------- | ------------------------------------------ |
| Sensor frames (`lidar_link`) and `gz_frame_id` | Placing laser scans correctly in the map  |
| `odom → base_footprint` from the diff-drive system | The odometry input to localization    |
| `use_sim_time`                               | Consistent timestamps for SLAM and Nav2    |
| Robot footprint and collision geometry       | Costmap inflation and collision avoidance  |
