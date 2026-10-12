# Course Syllabus

> **Course:** ROS 2 AMR & Manipulation
> **Duration:** 4 weeks · 48 contact hours

**Navigation:** [Course README](README.md) · **Syllabus** · [Learning Outcomes](learning_outcomes.md) · [Software](software_requirements.md) · [Assessment](assessment_policy.md)

---

## 1. Course Format

| Item              | Value                                   |
| ----------------- | --------------------------------------- |
| Weeks             | 4                                       |
| Lecture per week  | 4 hours                                 |
| Laboratory per week | 8 hours                               |
| Total contact time | 4 × (4 h + 8 h) = **48 hours**         |
| Final project     | Integrates all four weeks ([03_student_projects](../../03_student_projects/README.md)) |

---

## 2. Prerequisites

### Required knowledge

| Area        | Students should know                                                                    |
| ----------- | --------------------------------------------------------------------------------------- |
| Linux       | Terminal commands, file and directory management, installing packages, environment variables |
| Programming | Basic Python (functions, variables, classes, debugging); C/C++ fundamentals helpful     |
| Mathematics | Algebra, trigonometry, coordinate systems, vectors, matrices                            |
| Robotics    | Helpful but not required: robot, sensor, actuator, motor, coordinate frame              |
| Git         | `clone`, `status`, `add`, `commit`, `push`, `pull`                                      |

Advanced mathematics is **not** required before the course; kinematics is introduced progressively.

### Not required before the course

Previous experience with ROS 1, ROS 2, Gazebo, Nav2, SLAM Toolbox, MoveIt 2, or industrial robot programming. These are taught in the course.

### Preparation

Before Week 1, students should have Ubuntu 24.04, Git, a GitHub account, a code editor (e.g. VS Code), and a working ROS 2 Jazzy installation verified as described in [software_requirements.md](software_requirements.md). Later labs reuse the same environment, so a working setup in Week 1 saves time every week.

---

## 3. Weekly Schedule

### Week 1 — Linux, Middleware & ROS 2 Architecture

**Lecture topics:** Linux for robotics, processes, middleware (DDS), ROS 2 architecture, nodes, topics, services, actions, parameters, launch files, packages and workspaces, ROS graph, CLI tools.

**Laboratory:** Students build and inspect a small ROS 2 system: workspace → Python publisher/subscriber → service server/client → actions → parameters → PS4-controlled Turtlesim → launch file.

**Practical outcome:** a multi-node system started from one launch file and debugged with `rqt_graph`, `rqt_console`, and the ROS 2 CLI.

→ [Week 1](weeks/week01_ros2_architecture/README.md)

### Week 2 — Kinematic Modeling & Physics Simulation

**Lecture topics:** robot specification, links, joints, DoF, coordinate frames, transformations, forward kinematics, URDF, Xacro, joint origins, axes and limits, TF2, RViz2, visual/collision/inertial properties, Gazebo Harmonic physics, sensors, simulation time.

**Laboratory:** Students model a differential-drive robot step by step (URDF → Xacro → inertia → LiDAR), validate it, inspect its TF tree, verify forward kinematics, and drive it in Gazebo Harmonic. They then relate the model to the Trailobot description.

**Practical outcome:** a validated robot description that spawns and drives in Gazebo Harmonic, with LiDAR data and a complete TF tree.

→ [Week 2](weeks/week02_kinematics_simulation/README.md)

### Week 3 — SLAM & Autonomous Navigation

**Lecture topics:** mobile robot motion and sensors, LiDAR and `LaserScan`, occupancy grids, SLAM, localization, the `map → odom → base_link` relationship, costmaps, Nav2 architecture, global planning, local control, recovery behaviors.

**Laboratory (planned):** simulate Trailobot, build a map with SLAM, localize on the saved map, configure Nav2, send goals, and evaluate navigation behavior.

**Practical outcome (planned):** a simulated AMR that maps an environment and navigates autonomously to goals.

→ [Week 3](weeks/week03_slam_navigation/README.md) · *Lab guide not yet developed.*

### Week 4 — Manipulator Kinematics & Motion Planning

**Lecture topics:** 6-DoF manipulator structure, joint variables, joint-space and Cartesian motion, forward and inverse kinematics, Jacobian, singularities, end-effector and tool frames, trajectories, collision checking, MoveIt 2.

**Laboratory (planned):** load a manipulator description, inspect joints and tool frames, plan and execute joint-space and Cartesian motions with MoveIt 2 in simulation, add collision objects, and validate trajectories.

**Practical outcome (planned):** collision-free motion plans for a simulated industrial manipulator.

→ [Week 4](weeks/week04_manipulator_motion_planning/README.md) · *Lab guide not yet developed.*

---

## 4. Final Project

The final project integrates the four weeks:

```text
ROS 2 → Robot modeling → Simulation → Sensors → Navigation and/or Manipulation → Integrated system
```

Requirements, submission, and grading: [03_student_projects](../../03_student_projects/README.md).
