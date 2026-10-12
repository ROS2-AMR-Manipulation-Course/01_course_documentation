# RB3-1200 — Simulation Guide

**Navigation:** [Robot Simulation](../README.md) · [Trailobot](../trailobot_amr/README.md) · [ABB IRB 1200](../abb_irb1200/README.md) · **RB3-1200** · [RB3-1200 platform page](../../01_course_documentation/course/robot_platforms/rb3_1200_manipulator.md)

<p align="center">
  <img src="../../assets/images/robots/rb3_1200.png" alt="Rainbow Robotics RB3-1200 collaborative manipulator" height="240">
  <br><sub>RB3-1200</sub>
</p>

> **Status:** ⚠️ **Not yet available.** No RB3-1200 robot description, ROS 2 package, or simulation configuration has been supplied to the course repository yet.

---

## What Is Needed

To simulate the RB3-1200 in the course environment (ROS 2 Jazzy, Gazebo Harmonic, MoveIt 2), the following must be supplied by the course maintainer or a verified source:

1. A URDF/Xacro description with meshes, correct kinematic dimensions, and joint limits (with its license).
2. A `ros2_control` configuration for simulation (e.g. with `gz_ros2_control`).
3. A MoveIt 2 configuration package (SRDF, kinematics, joint limits, controllers). It can be generated with the MoveIt Setup Assistant once the URDF exists.
4. Launch files for visualization, simulation, and MoveIt 2.

The vendor's [rbpodo](https://github.com/RainbowRobotics/rbpodo) library is a client API for RB-series controllers. It does **not** provide a robot description or a simulation.

## Until Then

Week 4 manipulator simulation uses the [ABB IRB 1200](../abb_irb1200/README.md). The concepts (joint chain, tool frame, FK/IK, MoveIt 2 planning) carry over directly to the RB3-1200 once its description is available.

When the description is added, document here: the source repository and version, the build steps, the validation commands (`xacro`, `check_urdf`, zero-pose FK with `tf2_echo`), and the simulation and MoveIt 2 launch commands. Only document commands that have been tested.
