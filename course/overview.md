# ROS 2 AMR & Manipulation Course

## 1. Course Description

This course introduces students to the fundamental concepts and practical workflows used to develop robotic systems with ROS 2.

The course focuses on two major robot types:

* Autonomous Mobile Robots (AMRs)
* Industrial Robot Manipulators

Students will learn how to model, simulate, control, and develop basic autonomous behaviors for robots using modern robotics software.

The course combines theoretical knowledge with hands-on laboratory exercises using real-world robotics platforms and simulation.

## 2. Course Environment

The course uses the following main software environment:

* Ubuntu 24.04 LTS
* ROS 2 Jazzy
* Gazebo Harmonic
* RViz2
* SLAM Toolbox
* Nav2
* MoveIt 2

## 3. Robot Platforms

### Mobile Robot

**Trailobot**

A differential-drive AMR used for robot description, simulation, SLAM, localization, and autonomous navigation.

### Manipulators

**ABB IRB1200**

A 6-DOF industrial robot manipulator used for manipulator kinematics and motion planning.

**RB3-1200**

A 6-DOF industrial robot manipulator used for manipulator kinematics, simulation, and motion planning.

## 4. Course Structure

The course is divided into four weeks.

| Week | Topic                                    | Main Platform      |
| ---- | ---------------------------------------- | ------------------ |
| 1    | Linux, Middleware & ROS 2 Architecture   | ROS 2              |
| 2    | Kinematic Modeling & Physics Simulation  | Trailobot          |
| 3    | SLAM & Autonomous Navigation             | Trailobot          |
| 4    | Manipulator Kinematics & Motion Planning | IRB1200 / RB3-1200 |

Each week contains:

* 4 hours of lecture
* 8 hours of laboratory work

## 5. Learning Approach

The course follows a progressive robotics development workflow:

```text
Robotics Concept
       ↓
ROS 2 Architecture
       ↓
Robot Modeling
       ↓
Simulation
       ↓
Sensors
       ↓
Perception
       ↓
Navigation / Manipulation
       ↓
Experiment
       ↓
Integrated Robot Project
```

Students first learn the fundamental concepts and then apply them through practical ROS 2 projects.

## 6. Main Learning Areas

Students will study:

### ROS 2

* Nodes
* Topics
* Services
* Actions
* Parameters
* ROS 2 graph
* ROS 2 command-line tools
* Distributed ROS 2 communication

### Robot Modeling

* Links
* Joints
* Degrees of freedom
* Coordinate frames
* Transformations
* URDF
* Xacro
* TF2

### Simulation

* Gazebo Harmonic
* Physics simulation
* Collision models
* Inertial properties
* Sensors
* Robot controllers

### Mobile Robot Navigation

* LiDAR
* LaserScan
* Occupancy grids
* SLAM
* Localization
* Costmaps
* Global planning
* Local control
* Nav2

### Manipulator Robotics

* Forward kinematics
* Inverse kinematics
* Jacobian
* Singularity
* Trajectory
* Collision checking
* Motion planning
* MoveIt 2
* Pick-and-place

## 7. Course Goal

The main goal of this course is to give students a practical foundation for developing ROS 2-based robotic systems.

By completing the course, students should be able to understand how robot hardware, software, simulation, sensors, navigation, and manipulation components work together as one robotic system.

## 8. Related Project Repositories

The course documentation connects to the following project repositories:

```text
02_ros2_architecture
        ↓
03_trailobot
        ↓
04_manipulation
        ↓
05_final_project
```

The documentation explains the concepts and laboratory procedures, while the related repositories contain the ROS 2 implementation.

