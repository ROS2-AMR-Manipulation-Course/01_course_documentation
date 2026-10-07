# Environment Setup

## 1. Purpose

This document explains the required development environment for the ROS 2 AMR & Manipulation course.

All students should use the same primary environment:

```text
Ubuntu 24.04 LTS
        ↓
ROS 2 Jazzy
        ↓
Gazebo Harmonic
        ↓
RViz2
        ↓
SLAM Toolbox
        ↓
Nav2
        ↓
MoveIt 2
```

## 2. Recommended Computer

A computer used for this course should have sufficient CPU, RAM, GPU, and storage for ROS 2, Gazebo, RViz2, and robot simulation.

Recommended minimum:

* 4-core CPU
* 16 GB RAM
* 50 GB available storage
* Dedicated GPU recommended for heavier Gazebo simulations

More powerful hardware is recommended when running complex simulations.

## 3. Operating System

Install:

**Ubuntu 24.04 LTS 64-bit**

Students should verify the operating system:

```bash
lsb_release -a
```

## 4. ROS 2

Install:

**ROS 2 Jazzy Jalisco**

Verify the installation:

```bash
source /opt/ros/jazzy/setup.bash

ros2 --version
```

The ROS 2 environment should be sourced before using ROS 2 commands.

## 5. Create a ROS 2 Workspace

Create the course workspace:

```bash
mkdir -p ~/ros2_course_ws/src
cd ~/ros2_course_ws
```

Build the workspace:

```bash
colcon build
```

Source the workspace:

```bash
source install/setup.bash
```

## 6. Verify ROS 2

Open a terminal and run:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_cpp talker
```

Open another terminal:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_py listener
```

If the listener receives messages from the talker, the basic ROS 2 environment is working.

## 7. Gazebo

Verify that Gazebo Harmonic is installed and available.

The exact Gazebo/ROS integration used by the course should be verified during the course environment validation before students begin Week 2.

## 8. RViz2

Test RViz2:

```bash
source /opt/ros/jazzy/setup.bash
rviz2
```

RViz2 should open successfully.

## 9. Git

Verify Git:

```bash
git --version
```

Configure Git:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

## 10. GitHub

Students should have a GitHub account before beginning the practical projects.

They should be able to:

* Clone repositories
* Create branches
* Commit changes
* Push changes
* Pull updates

## 11. Course Repositories

Students will work with the following repositories:

```text
02_ros2_architecture
03_trailobot
04_manipulation
05_final_project
```

The course documentation repository provides the instructions for using these repositories.

## 12. Environment Verification

Before starting the first laboratory, students should verify:

```text
[ ] Ubuntu 24.04 installed
[ ] Git installed
[ ] GitHub account available
[ ] ROS 2 Jazzy installed
[ ] ROS 2 commands working
[ ] RViz2 working
[ ] Gazebo Harmonic working
[ ] ROS 2 workspace builds successfully
```

## 13. Troubleshooting

If the environment does not work correctly, students should first:

1. Check the Ubuntu version.
2. Check the ROS 2 installation.
3. Source the ROS 2 environment.
4. Check package installation.
5. Check workspace dependencies.
6. Rebuild the workspace.
7. Read the terminal error message carefully.
8. Consult the course troubleshooting guide.

Do not randomly reinstall the entire system before identifying the actual problem.
