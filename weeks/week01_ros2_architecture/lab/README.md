# Week 1 Lab — ROS 2 Architecture & Communication

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Lab Duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Turtlesim

**Navigation:** **Lab README** · [Lab Guide](lab_guide.md) · [Exercises](exercise.md) · [Lecture notes](../lecture/note.md)

---

## 1. Lab Overview

This laboratory introduces the basic ROS 2 development workflow. You start with command-line tools, write your own Python nodes, and finish by building a small system in which a **PS4 controller drives a simulated turtle** and a status node reports what the turtle is doing.

The objective is not only to run commands, but to understand **how ROS 2 components communicate** and how to **inspect and debug** a running system.

## 2. Learning Outcomes

By the end of this lab, students should be able to:

* Set up a ROS 2 environment, isolate it on a shared network, and build a workspace.
* Inspect a running system with `ros2 node`, `ros2 topic`, `ros2 service`, `ros2 action`, and `ros2 param`.
* Publish and subscribe to topics from the CLI and from Python nodes.
* Call services from the CLI and write a Python service server and client.
* Send, cancel, and preempt an action goal, and identify its goal, feedback, and result.
* Read, change, save, and load node parameters, and use parameters in their own nodes.
* Read PS4 controller input through `joy_node` and convert it into velocity commands.
* Install and start a multi-node system with a launch file and a YAML configuration file.
* Debug communication problems using `rqt_graph`, `rqt_console`, and CLI tools.
* Explain when to use a topic, a service, an action, or a parameter.

---

## 3. Lab Requirements

### Hardware

* Computer running Ubuntu 24.04 (native install recommended)
* PS4 (DualShock 4) controller with a USB cable, or Bluetooth
  * No controller? A software fallback is provided in the [Lab Guide, Section 10.3](lab_guide.md#103-fallback-simulate-a-controller).
  * Using a virtual machine? You must pass the controller's USB device through to the VM.

### Software

* ROS 2 Jazzy (desktop install), see [Environment Setup](../../../course/environment_setup.md)
* Git, Python 3, VS Code or another code editor
* Lab packages:

```bash
sudo apt update
sudo apt install -y \
  python3-colcon-common-extensions \
  ros-jazzy-turtlesim \
  ros-jazzy-joy \
  ros-jazzy-rqt-graph \
  ros-jazzy-rqt-console \
  ros-jazzy-example-interfaces
```

### Network

Each student must use a unique `ROS_DOMAIN_ID` (given by the instructor) so their nodes do not interfere with other students' nodes. See [Lab Guide, Section 2.2](lab_guide.md#22-isolate-your-ros-2-network-important-in-a-classroom).

### Basic Knowledge

Complete the Week 1 lecture before starting this laboratory. Students should be comfortable with:

* The Linux terminal and basic commands (`cd`, `ls`, `mkdir`, `nano`/`code`, `sudo apt`)
* Basic Python (classes, functions, f-strings)
* The ROS 2 concepts of node, topic, service, action, and parameter

---

## 4. Lab Workflow

| Step | Topic                              | Lab Guide section | Exercise |
| ---: | ---------------------------------- | :---------------: | :------: |
|    1 | Set up environment and workspace   |         2         |    1     |
|    2 | Inspect the Turtlesim system       |         3         |    2     |
|    3 | CLI publisher / subscriber         |         4         |    3     |
|    4 | Python publisher / subscriber      |         5         |    4     |
|    5 | Built-in services                  |         6         |    5     |
|    6 | Python service server and client   |         7         |    6     |
|    7 | Actions: goal, feedback, cancel    |         8         |    7     |
|    8 | Parameters                         |         9         |    8     |
|    9 | PS4 controller input (`/joy`)      |        10         |    9     |
|   10 | PS4 teleoperation node             |        11         |    10    |
|   11 | Turtle status node                 |        12         |    11    |
|   12 | Logs, ROS graph, and debugging     |        13         |    12    |
|   13 | Launch the complete system         |        14         |    13    |

**Recommended approach:** Read and follow a section of the Lab Guide, then complete the matching exercise before moving on.

---

## 5. Lab Files

| File                                                                 | Purpose                                                              |
| -------------------------------------------------------------------- | -------------------------------------------------------------------- |
| [`lab_guide.md`](lab_guide.md)                                       | Step-by-step guided instructions with complete example code          |
| [`exercise.md`](exercise.md)                                         | Exercises that extend the guide, submission format, and grading rubric |
| [`../lecture/note.md`](../lecture/note.md)                           | Week 1 lecture notes                                                 |
| [`../lecture/`](../lecture/)                                         | Week 1 lecture slides (PDF)                                          |

---

## 6. Expected Result

At the end of the laboratory, students should have:

* A ROS 2 workspace with three packages: `ros2_lab_basics`, `ros2_lab_services`, and `turtle_ps4_lab`.
* A PS4-controlled turtle with an enable (dead-man) button, started with **one launch command**.
* A `/turtle_status` topic reporting the turtle's position, motion state, and wall warnings.
* A short report explaining topics, services, actions, parameters, and launch files, and one problem they debugged.

Students should be able to inspect and debug a running ROS 2 system using the command line and ROS graph tools.

See [exercise.md — Submission](exercise.md#submission) for what to submit and how it is graded.

---

## 7. Next Step

After completing this laboratory:

**Week 2 — Kinematic Modeling & Physics Simulation**

Students will use ROS 2 to build and simulate the Trailobot model using:

* URDF
* Xacro
* TF2
* Gazebo Harmonic
* RViz2

The skills from this week — packages, nodes, topics, parameters, and launch files — are used in every following week.
