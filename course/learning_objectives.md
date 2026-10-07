# Learning Objectives

## 1. Course Learning Objectives

By the end of this course, students should be able to understand and apply the fundamental concepts required to develop basic ROS 2 robotic systems.

Students should be able to:

1. Explain the role of Linux, middleware, and ROS 2 in modern robotic systems.
2. Understand the main ROS 2 communication mechanisms.
3. Create and operate basic ROS 2 nodes.
4. Use ROS 2 topics, services, actions, and parameters.
5. Inspect and debug a ROS 2 system using command-line tools.
6. Model a robot using URDF and Xacro.
7. Understand coordinate frames and transformations using TF2.
8. Explain basic robot kinematics and degrees of freedom.
9. Simulate a robot in Gazebo Harmonic.
10. Configure and use simulated sensors.
11. Understand the basic SLAM workflow.
12. Build and use a map of a simulated environment.
13. Configure localization on a known map.
14. Understand the architecture of Nav2.
15. Configure basic autonomous navigation for a mobile robot.
16. Explain forward and inverse kinematics for manipulators.
17. Understand Jacobians and robot singularities.
18. Plan basic manipulator trajectories.
19. Use MoveIt 2 for manipulator motion planning.
20. Integrate multiple robotics components into a complete robotic workflow.

## 2. Week-by-Week Learning Outcomes

### Week 1 — Linux, Middleware & ROS 2 Architecture

Students should be able to:

* Explain why Linux is commonly used in robotics.
* Explain the purpose of middleware.
* Describe the architecture of ROS 2.
* Explain nodes, topics, services, actions, and parameters.
* Create and run basic ROS 2 nodes.
* Inspect ROS 2 topics and nodes using the CLI.
* Understand communication between distributed ROS 2 nodes.

### Week 2 — Kinematic Modeling & Physics Simulation

Students should be able to:

* Identify links, joints, and degrees of freedom.
* Define coordinate frames for a robot.
* Explain transformations between frames.
* Create a basic URDF/Xacro robot description.
* Understand the relationship between URDF and TF2.
* Configure a robot for Gazebo Harmonic.
* Add basic sensors to a simulated robot.
* Run the Trailobot in simulation.

### Week 3 — SLAM & Autonomous Navigation

Students should be able to:

* Explain how 2D LiDAR produces LaserScan data.
* Explain occupancy-grid maps.
* Describe the SLAM process.
* Generate a map using SLAM Toolbox.
* Explain localization on a known map.
* Understand the `map → odom → base_link` TF relationship.
* Explain global and local costmaps.
* Describe the main components of Nav2.
* Send navigation goals to a mobile robot.
* Evaluate basic navigation behavior.

### Week 4 — Manipulator Kinematics & Motion Planning

Students should be able to:

* Identify the structure and DoF of a 6-DOF industrial robot.
* Define manipulator coordinate frames.
* Explain joint variables.
* Calculate basic forward kinematics.
* Explain inverse kinematics.
* Understand the Jacobian matrix.
* Explain robot singularities.
* Understand trajectory generation.
* Explain the motion-planning workflow.
* Use MoveIt 2 for basic manipulator motion planning.
* Perform a basic simulated pick-and-place task.

## 3. Practical Competencies

Students should also develop the ability to:

* Read robotics documentation.
* Work with Git and GitHub.
* Debug ROS 2 systems.
* Analyze TF trees.
* Read ROS 2 logs and command-line output.
* Modify configuration files.
* Test robotic systems systematically.
* Document their experiments.
* Work collaboratively on robotics projects.
