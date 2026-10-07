# Course Schedule

## 1. Course Duration

The course is organized into four weeks.

Each week contains:

* 4 hours of lecture
* 8 hours of laboratory work

Total:

**48 contact hours**

```text
4 weeks × (4h lecture + 8h lab)
= 48 hours
```

## 2. Weekly Schedule

| Week | Lecture                                  | Laboratory           | Main Platform      |
| ---- | ---------------------------------------- | -------------------- | ------------------ |
| 1    | Linux, Middleware & ROS 2 Architecture   | ROS 2 fundamentals   | ROS 2              |
| 2    | Kinematic Modeling & Physics Simulation  | Trailobot simulation | Trailobot          |
| 3    | SLAM & Autonomous Navigation             | Mapping + Nav2       | Trailobot          |
| 4    | Manipulator Kinematics & Motion Planning | MoveIt 2             | IRB1200 / RB3-1200 |

## 3. Week 1

### Lecture

**Linux, Middleware & ROS 2 Architecture**

Topics:

* Linux for robotics
* Processes
* Middleware
* ROS 2 architecture
* Nodes
* Topics
* Services
* Actions
* Parameters
* ROS 2 graph
* ROS 2 CLI

### Laboratory

Students create and inspect basic ROS 2 systems.

Main activities:

```text
Workspace
   ↓
Node
   ↓
Topic
   ↓
Publisher / Subscriber
   ↓
Service
   ↓
Action
   ↓
ROS 2 Graph
```

## 4. Week 2

### Lecture

**Kinematic Modeling & Physics Simulation**

Topics:

* Robot specification
* Links
* Joints
* DoF
* Coordinate frames
* Transformations
* Kinematics
* URDF
* Xacro
* TF2
* Gazebo
* Physics
* Collision
* Inertia
* Sensors

### Laboratory

Students build and run the Trailobot simulation.

```text
URDF/Xacro
    ↓
Robot Model
    ↓
Gazebo Harmonic
    ↓
Sensors
    ↓
RViz2
```

## 5. Week 3

### Lecture

**SLAM & Autonomous Navigation**

Topics:

* LiDAR
* LaserScan
* Occupancy Grid
* SLAM
* Localization
* TF2
* Costmaps
* Nav2
* Global Planning
* Local Control
* Recovery

### Laboratory

Students:

1. Start Trailobot simulation.
2. Visualize LiDAR data.
3. Run SLAM.
4. Build a map.
5. Save the map.
6. Run localization.
7. Configure Nav2.
8. Send navigation goals.
9. Evaluate navigation behavior.

## 6. Week 4

### Lecture

**Manipulator Kinematics & Motion Planning**

Topics:

* Manipulator structure
* 6-DOF robots
* Coordinate frames
* Joint variables
* Forward kinematics
* Inverse kinematics
* Jacobian
* Singularity
* Trajectory
* Motion planning
* Collision checking
* MoveIt 2

### Laboratory

Students work with:

* ABB IRB1200
* RB3-1200

Main workflow:

```text
Robot Model
    ↓
Joint States
    ↓
Target Pose
    ↓
Inverse Kinematics
    ↓
Motion Planning
    ↓
Trajectory
    ↓
Simulation
```

## 7. Final Project

The final project integrates knowledge from all four weeks.

Students should apply:

```text
ROS 2
 ↓
Robot Modeling
 ↓
Simulation
 ↓
Sensors
 ↓
Navigation
 ↓
Manipulation
 ↓
Integrated Robot System
```

Detailed final-project requirements are provided separately.
