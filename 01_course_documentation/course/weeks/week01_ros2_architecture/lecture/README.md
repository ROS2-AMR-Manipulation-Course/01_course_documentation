# Week 1 Lecture — Linux, Middleware & ROS 2 Architecture

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Lecture:** 4 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy

**Navigation:** [Week 1 README](../README.md) · **Lecture README** · [Lecture Notes](notes.md) · [Lab](../lab/README.md)

---

## 1. Lecture Topic

A modern robot is not one large program: it is a set of software components that run as separate processes and communicate. This lecture explains why robots run on Linux, what middleware does, and how ROS 2 organizes robot software into nodes that communicate through topics, services, actions, and parameters. It uses the Trailobot AMR as a running example.

## 2. Learning Objectives

After the lecture, students should be able to:

1. Explain why Linux is commonly used in robotics, and define a process.
2. Explain the purpose of middleware and what ROS 2 is (and is not).
3. Define a ROS 2 node and describe the ROS 2 architecture.
4. Explain topics, services, actions, and parameters, and choose the right one for a task.
5. Describe the ROS 2 graph and use basic ROS 2 CLI commands to inspect it.
6. Explain launch files, packages, and workspaces.
7. Explain a distributed ROS 2 system and connect these concepts to an AMR.

## 3. Lecture Materials

| Material                    | Description                                                                 |
| --------------------------- | --------------------------------------------------------------------------- |
| Slides                      | [`Week1-Linux,Middleware&ROS2_Architecture.pdf`](Week1-Linux%2CMiddleware%26ROS2_Architecture.pdf), uploaded by the course maintainer |
| [Lecture notes](notes.md)   | Full written notes: Linux and processes, middleware, ROS 2 architecture, communication patterns, ROS graph, CLI, launch, distributed ROS 2, Trailobot architecture, knowledge check |

**About the slides:** the slides are the visual summary used in class. The lecture notes are the complete written reference. Slides are prepared and uploaded by the course maintainer. If a `slides.pdf` file is added later, it is the current version of the slides.

## 4. Lecture Plan (4 hours)

| Part | Topic                                         | Notes section                                   |
| ---- | --------------------------------------------- | ----------------------------------------------- |
| 1    | Robotics software foundation; hardware to software | Sections 3–4                               |
| 2    | Linux and processes                           | Section 5                                       |
| 3    | Middleware                                    | Section 6                                       |
| 4    | ROS 2: architecture, nodes, Trailobot as a node system | Sections 7–10                          |
| 5    | Topics, services, actions, parameters, ROS graph, CLI, launch, distributed ROS 2 | Sections 11–22 |
| —    | Connections to Weeks 2–4, knowledge check     | Sections 23–30                                  |

## 5. Knowledge Check

The lecture notes end with a knowledge check (Section 26) and the Week 1 completion criteria (Section 29). Students should be able to answer them before the lab.
