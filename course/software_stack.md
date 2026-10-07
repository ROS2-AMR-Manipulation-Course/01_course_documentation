# Software Stack

## 1. Official Course Environment

The course uses a common software environment so that students can reproduce the laboratory exercises consistently.

| Component        | Version          |
| ---------------- | ---------------- |
| Operating System | Ubuntu 24.04 LTS |
| ROS 2            | Jazzy Jalisco    |
| Gazebo           | Harmonic         |
| Visualization    | RViz2            |
| Navigation       | Nav2             |
| SLAM             | SLAM Toolbox     |
| Manipulation     | MoveIt 2         |
| Version Control  | Git / GitHub     |

## 2. Software Architecture

```text
Ubuntu 24.04 LTS
        │
        ▼
   ROS 2 Jazzy
        │
 ┌──────┼───────────────┐
 │      │               │
 ▼      ▼               ▼
RViz2  Gazebo        ROS 2 Tools
       Harmonic
 │      │
 │      ├── Robot Simulation
 │      ├── Physics
 │      └── Sensors
 │
 ├── SLAM Toolbox
 │
 ├── Nav2
 │
 └── MoveIt 2
```

## 3. ROS 2

ROS 2 provides the middleware and software framework used to connect robot components.

Students will use:

* Nodes
* Topics
* Services
* Actions
* Parameters
* TF2
* Launch
* ROS 2 CLI

## 4. Gazebo Harmonic

Gazebo Harmonic is used for physics-based robot simulation.

Students will use Gazebo to simulate:

* Robot motion
* Physics
* Collision
* Sensors
* Robot environments

The Trailobot simulation is the main mobile-robot simulation platform.

## 5. RViz2

RViz2 is used to visualize ROS 2 data and robot states.

Students will use RViz2 to visualize:

* Robot models
* TF frames
* LiDAR data
* Maps
* Robot pose
* Navigation information
* Manipulator states

## 6. SLAM Toolbox

SLAM Toolbox is used during Week 3 to introduce 2D SLAM.

Main workflow:

```text
LiDAR
  ↓
LaserScan
  ↓
SLAM Toolbox
  ↓
Occupancy Grid Map
```

## 7. Nav2

Nav2 provides the autonomous navigation framework for the Trailobot.

Main workflow:

```text
Map
 ↓
Localization
 ↓
Navigation Goal
 ↓
Global Planner
 ↓
Local Controller
 ↓
Velocity Command
 ↓
Trailobot
```

## 8. MoveIt 2

MoveIt 2 is used for manipulator motion planning.

Main workflow:

```text
Robot Model
    ↓
Joint State
    ↓
Target Pose
    ↓
Kinematics
    ↓
Motion Planning
    ↓
Collision Checking
    ↓
Trajectory
    ↓
Robot
```

## 9. Development Tools

Students may use:

* Git
* GitHub
* VS Code
* Terminal
* Python
* C++

## 10. Course Robot Platforms

### Trailobot

```text
Differential Drive AMR
        │
        ├── URDF/Xacro
        ├── Gazebo
        ├── LiDAR
        ├── SLAM
        ├── Localization
        └── Nav2
```

### Industrial Manipulators

```text
6-DOF Robot
    │
    ├── ABB IRB1200
    │
    └── RB3-1200
            │
            ├── Kinematics
            ├── Trajectory
            └── MoveIt 2
```
