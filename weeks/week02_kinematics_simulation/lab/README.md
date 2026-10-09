# Week 2 Lab — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Lab Duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** **Lab README** · [Lab Guide](lab_guide.md) · [Exercises](exercises.md) · [Lecture Notes](../lecture/notes.md) · [Week 2 README](../README.md)

---

## 1. Lab Overview

In this laboratory you build a small differential-drive robot, **`diffbot`**, from a written specification. You describe it in URDF, refactor it with Xacro, add collision and inertial properties and a LiDAR, and check it with validation and TF2 tools. Finally you drive it in **Gazebo Harmonic**.

`diffbot` has the same structure as the course robot **Trailobot**: two driven wheels, a caster, a chassis, and a 2D LiDAR. The skills from this lab apply directly to the Trailobot model, and to SLAM and navigation in Week 3.

The objective is not only to make the robot appear in a simulator. You should understand **why** each part of the model is there and be able to **diagnose** a model that does not work.

---

## 2. Learning Outcomes

By the end of this lab, students should be able to:

* Write a URDF with links and fixed and continuous joints, including correct joint origins and axes.
* Validate a robot description with `check_urdf`, `urdf_to_graphviz`, and `gz sdf -p`.
* Visualize a model in RViz2 and test its joints with `joint_state_publisher_gui`.
* Refactor a URDF into Xacro with properties, math expressions, macros, and includes.
* Add collision geometry and calculate inertial properties for primitive shapes.
* Inspect a TF tree with `view_frames` and `tf2_echo`, and verify forward kinematics by hand.
* Configure a robot for Gazebo Harmonic: the diff-drive and joint-state systems, a GPU LiDAR, a world file, and `ros_gz_bridge`.
* Spawn and drive the robot in Gazebo Harmonic from one launch file, using simulation time.
* Explain the effect of friction, centre of mass, inertia, and collision geometry on the simulated robot.
* Diagnose invalid URDFs, wrong joint axes, bad inertial values, missing transforms, and spawn failures.

---

## 3. Lab Requirements

### Hardware

* Computer running Ubuntu 24.04 (native install recommended)
* A GPU with working OpenGL drivers is recommended for Gazebo
  * Using a virtual machine? Enable 3D acceleration. See [Lab Guide, Section 11.6](lab_guide.md#116-gazebo-spawn-failures) if Gazebo does not render.

### Software

* ROS 2 Jazzy (desktop install) with the Week 1 workspace `~/ros2_ws`
* Lab packages:

```bash
sudo apt update
sudo apt install -y \
  ros-jazzy-xacro \
  ros-jazzy-robot-state-publisher \
  ros-jazzy-joint-state-publisher-gui \
  ros-jazzy-rviz2 \
  ros-jazzy-tf2-tools \
  ros-jazzy-ros-gz \
  ros-jazzy-teleop-twist-keyboard \
  liburdfdom-tools
```

`ros-jazzy-ros-gz` installs **Gazebo Harmonic** as ROS vendor packages. Do not install Gazebo Classic.

### Network

Keep the unique `ROS_DOMAIN_ID` from Week 1 in your `~/.bashrc`.

### Basic Knowledge

Complete the Week 2 lecture ([notes](../lecture/notes.md) and [kinematics examples](../lecture/kinematics_examples.md)) before starting. Students should be comfortable with:

* The Week 1 skills: workspaces, `colcon build`, sourcing, topics, and launch files
* Links, joints, DoF, coordinate frames, and forward kinematics (Week 2 lecture)
* Basic XML syntax (elements, attributes, closing tags)

---

## 4. Lab Workflow

| Step | Topic                                         | Lab Guide section | Exercise |
| ---: | --------------------------------------------- | :---------------: | :------: |
|    1 | Specification, frames, DoF                    |         4         |    1     |
|    2 | Package + first URDF + validation             |        3–4        |    2     |
|    3 | RViz2, joint sliders, joint axes              |         5         |    3     |
|    4 | Xacro and parametric design                   |         6         |    4     |
|    5 | Collision and inertial properties             |         7         |    5     |
|    6 | LiDAR link, TF2, forward kinematics           |         8         |    6     |
|    7 | Gazebo plugins, world, bridge, launch, drive  |      9–10.3       |    7     |
|    8 | Odometry vs. the kinematic model              |     10.4–10.6     |    8     |
|    9 | Physics experiments                           |        11.4       |    9     |
|   10 | LiDAR configuration                           |     9.1, 10.4     |    10    |
|   11 | Debugging challenge                           |        11         |    11    |

**Recommended approach:** Read and follow a section of the Lab Guide, then complete the matching exercise before moving on. Validate the model after **every** change.

---

## 5. Lab Files

| File                                                                    | Purpose                                                            |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------ |
| [`lab_guide.md`](lab_guide.md)                                          | Step-by-step instructions with every package file, plus troubleshooting |
| [`exercises.md`](exercises.md)                                          | Exercises, expected outcomes, submission format, grading rubric    |
| [`../lecture/notes.md`](../lecture/notes.md)                            | Week 2 lecture notes                                               |
| [`../lecture/kinematics_examples.md`](../lecture/kinematics_examples.md) | Worked examples: transforms, forward kinematics, inertia          |
| [`../assessment/practical_assessment.md`](../assessment/practical_assessment.md) | The practical demonstration at the end of the lab        |
| [`../resources/reference_links.md`](../resources/reference_links.md)    | Official ROS 2 Jazzy and Gazebo Harmonic documentation             |

---

## 6. Expected Result

At the end of the laboratory, students should have:

* A `diffbot_description` package that builds with `colcon build`, containing a validated Xacro model, two launch files, a world, and a bridge configuration.
* A robot that spawns in Gazebo Harmonic with one launch command, drives with `/cmd_vel`, and publishes `/odom`, `/joint_states`, `/scan`, and a complete TF tree from `odom` to `lidar_link`.
* Hand calculations of transforms, forward kinematics, and inertia, each checked against TF2 or the generated URDF.
* A short report explaining the physics experiments and one problem they debugged.

See [exercises.md — Submission](exercises.md#submission) for what to submit and how it is graded.

---

## 7. Next Step

After completing this laboratory:

**Week 3 — SLAM & Autonomous Navigation**

Students will use the simulated mobile robot and its LiDAR to:

* Build a map with SLAM Toolbox
* Localize the robot on the map (`map → odom`)
* Navigate autonomously with Nav2

The model, the `odom → base_footprint` transform, `/scan`, and `use_sim_time` from this week are the inputs Week 3 builds on.
