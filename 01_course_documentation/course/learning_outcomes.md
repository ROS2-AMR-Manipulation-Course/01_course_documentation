# Learning Outcomes

**Navigation:** [Course README](README.md) · [Syllabus](course_syllabus.md) · **Learning Outcomes** · [Software](software_requirements.md) · [Assessment](assessment_policy.md)

---

Each outcome starts with an observable action, so it can be checked in a lab, exercise, quiz, or demonstration. "Evidence" shows where it is assessed.

## 1. Course Learning Outcomes

By the end of the course, students will be able to:

| #  | Outcome                                                                                              | Week |
| -- | ---------------------------------------------------------------------------------------------------- | :--: |
| 1  | Explain the role of Linux, middleware, and ROS 2 in a robotic system.                               | 1    |
| 2  | Build ROS 2 nodes that communicate through topics, services, actions, and parameters.              | 1    |
| 3  | Start, inspect, and debug a multi-node ROS 2 system with launch files and CLI/rqt tools.            | 1    |
| 4  | Identify links, joints, and degrees of freedom of a robot and define its coordinate frames.         | 2    |
| 5  | Compute forward kinematics for a planar arm and a differential-drive robot, and verify it with TF2. | 2    |
| 6  | Write and validate a URDF/Xacro description with visual, collision, and inertial properties.        | 2    |
| 7  | Simulate a robot with sensors in Gazebo Harmonic and connect it to ROS 2.                           | 2    |
| 8  | Build a map with SLAM and localize a robot on a known map.                                          | 3    |
| 9  | Configure and evaluate autonomous navigation with Nav2.                                             | 3    |
| 10 | Explain forward and inverse kinematics, Jacobians, and singularities of a 6-DoF manipulator.        | 4    |
| 11 | Plan and validate collision-free manipulator motion with MoveIt 2.                                  | 4    |
| 12 | Integrate several robotics components into one documented, reproducible system.                    | Project |

---

## 2. Week-by-Week Outcomes

### Week 1 — Linux, Middleware & ROS 2 Architecture

Students will be able to:

* Explain why Linux and middleware are used in robotics.
* Describe the ROS 2 architecture and the role of nodes, topics, services, actions, and parameters.
* Create, build, and run Python ROS 2 nodes in a `colcon` workspace.
* Choose between a topic, a service, an action, and a parameter for a given robot task, and justify the choice.
* Inspect a running system with `ros2 node`, `ros2 topic`, `ros2 service`, `ros2 action`, and `ros2 param`.
* Start a multi-node system with a launch file and diagnose communication problems.

**Evidence:** [Week 1 exercises](weeks/week01_ros2_architecture/lab/exercise.md) and demonstration.

### Week 2 — Kinematic Modeling & Physics Simulation

Students will be able to:

* Identify links, joint types (fixed, revolute, continuous, prismatic), and the DoF of a robot.
* Define robot frames following REP 103 and REP 105 (`odom → base_footprint → base_link → sensors`).
* Compose transformations and compute forward kinematics by hand, and match the result with `tf2_echo` to 3 decimal places.
* Write a URDF with correct joint origins, axes, and limits, and refactor it into Xacro with properties and macros.
* Calculate inertia tensors for primitive shapes and add collision and inertial properties.
* Validate a model with `check_urdf` and `gz sdf -p`, and explain fixed-joint lumping.
* Spawn and drive a robot in Gazebo Harmonic through `ros_gz_sim` and `ros_gz_bridge` using simulation time.
* Diagnose invalid URDFs, wrong joint axes, bad inertial values, missing transforms, and spawn failures.

**Evidence:** [Week 2 exercises](weeks/week02_kinematics_simulation/lab/exercise.md), review quiz, and practical demonstration.

### Week 3 — SLAM & Autonomous Navigation

Students will be able to:

* Explain how a 2D LiDAR produces `LaserScan` data and how an occupancy grid represents space.
* Describe the SLAM process and generate a map of a simulated environment.
* Explain localization on a known map and the `map → odom → base_link` TF relationship.
* Explain global and local costmaps and the main components of Nav2.
* Send navigation goals and evaluate navigation behavior with measurable criteria (goal reached, path, time, collisions).

**Evidence:** planned Week 3 lab (not yet developed).

### Week 4 — Manipulator Kinematics & Motion Planning

Students will be able to:

* Identify the joints, DoF, and frames (base, flange, tool) of a 6-DoF industrial manipulator.
* Compute forward kinematics and explain inverse kinematics and multiple IK solutions.
* Explain the Jacobian and identify singular configurations.
* Distinguish joint-space and Cartesian motion.
* Plan, check for collisions, and validate trajectories with MoveIt 2 in simulation.

**Evidence:** planned Week 4 lab (not yet developed).

---

## 3. Practical Competencies

Throughout the course, students develop the ability to:

* Read official robotics documentation ([04_references](../../04_references/README.md)).
* Use Git and GitHub for their work.
* Debug ROS 2 systems systematically and analyze TF trees.
* Read logs and command-line output.
* Modify configuration files safely.
* Test robotic systems and document experiments reproducibly.
* Work collaboratively on robotics projects.
