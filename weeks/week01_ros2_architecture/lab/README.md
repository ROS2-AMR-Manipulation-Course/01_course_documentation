# Week 1 Lab — ROS 2 Architecture & Communication

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Lab Duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy

---

## 1. Lab Overview

This laboratory introduces students to the basic ROS 2 development workflow.

Students will move from:

```text
Linux Terminal
      ↓
ROS 2 Workspace
      ↓
ROS 2 Nodes
      ↓
Topics
      ↓
Services
      ↓
Actions
      ↓
Parameters
      ↓
ROS Graph
```

The objective is not only to run commands, but to understand how ROS 2 components communicate.

---

## 2. Learning Outcomes

By the end of this lab, students should be able to:

* Create a ROS 2 workspace.
* Build and source a ROS 2 workspace.
* Run ROS 2 nodes.
* Inspect running nodes.
* Inspect topics.
* Read topic data.
* Publish topic data.
* Inspect services.
* Inspect actions.
* Inspect parameters.
* Visualize the ROS 2 graph.
* Explain the communication relationships between nodes.

---

## 3. Lab Requirements

### Software

* Ubuntu 24.04 LTS
* ROS 2 Jazzy
* Git
* Python 3
* VS Code or another code editor
* RViz2
* rqt / rqt_graph

### Basic Knowledge

Students should understand:

* Linux terminal
* Basic Linux commands
* ROS 2 node
* Topic
* Service
* Action
* Parameter

Complete the Week 1 lecture before starting this laboratory.

---

## 4. Lab Workflow

```text
01. Setup Environment
        ↓
02. Create Workspace
        ↓
03. Run ROS 2 Examples
        ↓
04. Explore Nodes
        ↓
05. Explore Topics
        ↓
06. Publisher / Subscriber
        ↓
07. Explore Services
        ↓
08. Explore Actions
        ↓
09. Explore Parameters
        ↓
10. Visualize ROS Graph
        ↓
11. Mini ROS 2 System
        ↓
12. Exercises
```

---

## 5. Lab Files

| File           | Purpose                          |
| -------------- | -------------------------------- |
| `lab_guide.md` | Step-by-step laboratory          |
| `exercises.md` | Student exercises and challenges |
| `slides.pdf`   | Instructor lab presentation      |

---

## 6. Expected Result

At the end of the laboratory, students should have a working ROS 2 workspace and understand how multiple ROS 2 nodes communicate.

Students should be able to inspect a running ROS 2 system using the command line and ROS graph tools.

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
