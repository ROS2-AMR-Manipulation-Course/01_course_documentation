# Week 1 Lecture Notes — Linux, Middleware & ROS 2 Architecture

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Lecture:** 4 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

---

# 1. Lecture Purpose

The purpose of this lecture is to establish the software foundation required for the rest of the course.

Students should understand that a modern robot is not one large program.

Instead, it is a collection of software components that perform different functions and communicate with each other.

A simplified model is:

```text
Sensors
   ↓
Robot Software
   ↓
ROS 2
   ↓
Algorithms
   ↓
Controllers
   ↓
Actuators
```

The lecture moves from low-level concepts to the complete ROS 2 robot system:

```text
Linux
  ↓
Process
  ↓
Middleware
  ↓
ROS 2
  ↓
Node
  ↓
Topic / Service / Action
  ↓
ROS Graph
  ↓
Complete Robot
```

---

# 2. Learning Objectives

After the lecture, students should be able to:

1. Explain why Linux is commonly used in robotics.
2. Define a process.
3. Explain the purpose of middleware.
4. Explain what ROS 2 is and what it is not.
5. Define a ROS 2 node.
6. Explain topics, services, actions, and parameters.
7. Understand the ROS 2 graph.
8. Use basic ROS 2 CLI commands.
9. Explain a distributed ROS 2 system.
10. Connect these concepts to an actual AMR system.

---

# 3. Part 1 — Robotics Software Foundation

## 3.1 Why Does a Robot Need Software?

Start with a real Trailobot photo.

Ask students:

> "What can this robot do if we remove all of its software?"

Expected answer:

> Almost nothing useful.

A robot requires software to:

* Read sensors.
* Estimate its state.
* Build maps.
* Localize itself.
* Plan motion.
* Control motors.
* Communicate with other components.
* Monitor the system.

For Trailobot:

```text
LiDAR
  ↓
Perception / SLAM
  ↓
Map + Robot Pose
  ↓
Navigation
  ↓
Velocity Command
  ↓
Motor Controller
  ↓
Wheels
```

### Teaching point

Hardware gives the robot physical capability.

Software gives the robot intelligence and behavior.

---

# 4. From Hardware to Software

A useful architecture is:

```text
Application
     ↓
Robot Software
     ↓
ROS 2
     ↓
Middleware
     ↓
Operating System
     ↓
Hardware
```

Explain that each layer hides some complexity from the layer above it.

For example:

A navigation algorithm should not need to directly control electrical signals going to a motor driver.

Instead:

```text
Navigation
    ↓
ROS 2 message
    ↓
Controller
    ↓
Hardware interface
    ↓
Motor
```

This separation makes robot software easier to develop and maintain.

---

# 5. Part 2 — Linux & Processes

## 5.1 Why Linux?

Explain that robotics development often requires:

* Hardware interfaces
* Networking
* Device drivers
* Development tools
* Compilers
* Python
* C++
* Containers
* GPU computing
* ROS 2

Ubuntu provides a practical development environment for these requirements.

For this course:

```text
Ubuntu 24.04 LTS
        ↓
ROS 2 Jazzy
        ↓
Gazebo Harmonic
        ↓
RViz2 / Nav2 / MoveIt 2
```

---

## 5.2 What Is a Process?

Define:

> A process is a program that is currently executing.

Example:

```text
Executable:
lidar_driver

        ↓

Running process:
LiDAR driver process
```

A process has resources such as:

* CPU time
* Memory
* File descriptors
* Network resources

---

## 5.3 Multiple Processes

A robot can run many processes simultaneously.

Example:

```text
LiDAR Driver
Camera Driver
SLAM
Localization
Nav2
Controller
Diagnostics
RViz2
```

This creates a new problem:

> How do these independent programs communicate?

This leads naturally to middleware.

---

# 6. Part 3 — Middleware

## 6.1 Definition

Middleware is software that provides common communication and coordination mechanisms between independent software components.

In robotics, middleware can handle things such as:

* Message transport
* Discovery
* Serialization
* Networking
* Communication abstraction

---

## 6.2 Why Not Direct Communication?

Imagine each component communicates directly with every other component.

```text
A ↔ B
A ↔ C
A ↔ D
B ↔ C
B ↔ D
C ↔ D
```

As the number of components grows, the system becomes difficult to manage.

A common communication layer reduces this complexity.

```text
       A
       │
       ▼
B → Middleware ← C
       ▲
       │
       D
```

---

# 7. Part 4 — ROS 2

## 7.1 What Is ROS 2?

Explain carefully:

> ROS 2 is a robotics software framework and ecosystem for developing distributed robot applications.

ROS 2 provides:

* Communication APIs
* Tools
* Libraries
* Message definitions
* Package structure
* Launch system
* Parameters
* Lifecycle mechanisms
* Simulation integration
* Visualization and debugging tools

### Important correction

ROS 2 is **not** an operating system.

The operating system in this course is:

> Ubuntu 24.04 Linux.

ROS 2 runs on top of Linux.

---

# 8. ROS 2 Architecture

Use this simplified model:

```text
┌─────────────────────────────┐
│     Robot Applications      │
│  SLAM │ Nav2 │ MoveIt 2     │
├─────────────────────────────┤
│            ROS 2            │
│ Nodes │ Topics │ Actions    │
├─────────────────────────────┤
│       Middleware / DDS      │
├─────────────────────────────┤
│       Ubuntu Linux          │
├─────────────────────────────┤
│ Hardware / Network / GPU    │
└─────────────────────────────┘
```

Explain that DDS is one of the middleware technologies used by ROS 2.

Avoid going too deeply into DDS implementation details in Week 1.

The objective is simply:

> ROS 2 provides robotics APIs and concepts while middleware provides the underlying communication mechanism.

---

# 9. What Is a ROS 2 Node?

A node is a software component that performs a particular function within a ROS 2 system.

Examples:

```text
/lidar_node
/slam_node
/localization_node
/controller_node
```

A node can:

* Publish topics
* Subscribe to topics
* Provide services
* Call services
* Provide actions
* Send action goals
* Use parameters

### Important clarification

Do not teach:

> "One node = exactly one process."

Instead say:

> A node is a ROS 2 software entity. Nodes may run in separate processes or be composed into the same process.

For beginners, it is fine to initially visualize nodes as independent components.

---

# 10. Trailobot as a Node System

Use the real Trailobot photo.

Build the conceptual system:

```text
             Trailobot
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
     LiDAR       IMU    Encoders
      Node       Node      Node
       │          │         │
       └──────────┼─────────┘
                  ↓
             Localization
                  ↓
                Nav2
                  ↓
             Controller
                  ↓
                Motors
```

Explain:

> The robot is not one ROS 2 node.

It is a system composed of many components.

---

# 11. Part 5 — Topics

## 11.1 What Is a Topic?

A topic is a named communication channel used for asynchronous data exchange.

Typical use cases:

* Sensor data
* Robot state
* Odometry
* Velocity commands
* Joint states

Example:

```text
LiDAR Node
    │
    │ publishes
    ▼
 /scan
    │
    ├────► SLAM
    │
    └────► RViz2
```

---

## 11.2 Publisher and Subscriber

A publisher sends messages.

A subscriber receives messages.

Example:

```text
Publisher
LiDAR Node
    │
    ▼
 /scan
    │
    ▼
Subscriber
SLAM Node
```

One topic can have multiple publishers and subscribers, depending on the system design.

---

# 12. Common ROS 2 Topics

Introduce familiar examples:

```text
/scan
/imu/data
/odom
/cmd_vel
/joint_states
/tf
/tf_static
```

Explain their general meaning.

### `/scan`

Usually LaserScan data.

### `/odom`

Odometry estimate.

### `/cmd_vel`

Velocity command.

### `/joint_states`

Joint position, velocity, and effort information.

### `/tf`

Dynamic coordinate-frame transforms.

### `/tf_static`

Static coordinate-frame transforms.

---

# 13. Service

A service provides request/response communication.

```text
Client
  │
  │ Request
  ▼
Service Server
  │
  │ Response
  ▼
Client
```

Good for relatively quick operations.

Example:

```text
/save_map
```

The user requests an operation and receives a response.

---

# 14. Action

An action is designed for long-running tasks.

Example:

> Navigate Trailobot to a goal pose.

The action can provide:

```text
Goal
 ↓
Execution
 ↓
Feedback
 ↓
Result
```

It can also support cancellation.

This makes actions suitable for tasks such as:

* Navigation
* Manipulator motion
* Long-running robot behaviors

---

# 15. Topic vs Service vs Action

Teach this simple rule:

```text
TOPIC
"What data is being exchanged?"
        ↓
Continuous / asynchronous data


SERVICE
"Please perform this quick operation."
        ↓
Request → Response


ACTION
"Please perform this task."
        ↓
Goal → Feedback → Result
```

This is more useful to students than memorizing definitions.

---

# 16. Parameters

Parameters configure node behavior.

Example:

```text
use_sim_time: true
max_velocity: 0.5
robot_radius: 0.25
```

Explain:

> A parameter is configuration, not a replacement for normal runtime communication.

Compare:

```text
Parameter
   ↓
Configuration


Topic
   ↓
Runtime data
```

---

# 17. ROS 2 Graph

The ROS graph represents the relationships between ROS 2 entities.

Example:

```text
LiDAR
  │
 /scan
  ↓
SLAM
  │
 /map
  ↓
Nav2
  │
/cmd_vel
  ↓
Controller
```

Explain that the graph is useful for:

* Understanding system architecture
* Debugging
* Finding missing connections
* Checking publishers/subscribers

---

# 18. ROS 2 CLI

Introduce the CLI as the main debugging tool.

### Nodes

```bash
ros2 node list
ros2 node info /node_name
```

### Topics

```bash
ros2 topic list
ros2 topic info /scan
ros2 topic echo /scan
```

### Services

```bash
ros2 service list
```

### Actions

```bash
ros2 action list
```

### Parameters

```bash
ros2 param list
```

---

# 19. Demonstration — Inspect a ROS 2 System

During the lecture, demonstrate:

```bash
ros2 node list
```

Ask:

> How many nodes are running?

Then:

```bash
ros2 topic list
```

Ask:

> What information is being exchanged?

Then:

```bash
ros2 topic info /scan
```

Ask:

> Who publishes `/scan`?

Finally:

```bash
ros2 topic echo /scan
```

Ask:

> What does the actual message look like?

This demonstration connects theory to practice.

---

# 20. Launch System

Explain the problem with manually starting many processes.

Without launch:

```text
Terminal 1 → LiDAR
Terminal 2 → Robot State
Terminal 3 → SLAM
Terminal 4 → Nav2
Terminal 5 → RViz2
```

With launch:

```text
launch.py
   │
   ├── LiDAR
   ├── Robot State
   ├── SLAM
   ├── Nav2
   └── RViz2
```

Example:

```bash
ros2 launch <package> <launch_file>
```

The launch system becomes increasingly important in Weeks 2–4.

---

# 21. Distributed ROS 2

ROS 2 can communicate between computers.

Example:

```text
                 Network
                    │
        ┌───────────┴───────────┐
        ↓                       ↓
    Robot PC              Development PC
        │                       │
    Sensors                   RViz2
    Controllers               Debugging
    Navigation                Development
```

This is important for real robots because computational workloads can be distributed.

For example:

* Robot computer handles hardware and control.
* Development computer runs visualization and development tools.

---

# 22. Complete Trailobot Architecture

End the lecture by returning to the real robot.

```text
                    TRAILOBOT
                        │
        ┌───────────────┼───────────────┐
        ↓               ↓               ↓
      LiDAR             IMU          Encoders
        │               │               │
        └───────────────┼───────────────┘
                        ↓
                       ROS 2
                        │
               ┌────────┴────────┐
               ↓                 ↓
              SLAM            Localization
                                  │
                                  ↓
                                 Nav2
                                  │
                              /cmd_vel
                                  │
                                  ↓
                              Controller
                                  │
                                  ↓
                                Motors
```

Tell students:

> "Everything we learned today exists because these components need to communicate."

---

# 23. Connection to Week 2

Week 1 answers:

> How do robot software components communicate?

Week 2 asks:

> How do we describe the robot itself?

The next concepts are:

```text
URDF
Xacro
Links
Joints
TF2
Gazebo
Sensors
Physics
```

Then we can move from:

```text
ROS 2 Software
```

to:

```text
ROS 2 + Robot Model + Simulation
```

---

# 24. Connection to Week 3

Week 3 builds on the Trailobot system.

```text
LiDAR
  ↓
/scan
  ↓
SLAM
  ↓
Map
  ↓
Localization
  ↓
Nav2
  ↓
/cmd_vel
  ↓
Controller
  ↓
Motors
```

Students should already understand the communication structure before learning navigation.

---

# 25. Connection to Week 4

Week 4 applies the same ROS 2 architecture to manipulation.

```text
RB3-1200
    ↓
Joint States
    ↓
Robot State
    ↓
MoveIt 2
    ↓
Motion Planning
    ↓
Trajectory
    ↓
Controller
    ↓
Robot Joints
```

The robot type changes, but the ROS 2 concepts remain.

---

# 26. Knowledge Check

Ask students:

### Question 1

What is the role of Linux?

### Question 2

What is a process?

### Question 3

Why do robots need middleware?

### Question 4

Is ROS 2 an operating system?

### Question 5

What is a ROS 2 node?

### Question 6

When should we use a topic?

### Question 7

What is the difference between a service and an action?

### Question 8

What is a parameter?

### Question 9

What does the ROS graph represent?

### Question 10

How can we inspect a running ROS 2 system?

---

# 27. Key Teaching Message

Students should leave Week 1 with this mental model:

```text
             ROBOT
               │
       ┌───────┴────────┐
       │                │
    Hardware         Software
       │                │
 Sensors/Actuators    Linux
                        │
                     Processes
                        │
                    Middleware
                        │
                      ROS 2
                        │
              ┌─────────┼─────────┐
              ↓         ↓         ↓
            Nodes     Topics   Actions
                        │
                        ↓
                   Robot System
```

The key message is:

> **A robot is a distributed software system running on hardware.**

ROS 2 provides the tools and communication concepts that allow us to build that system.

---

# 28. Instructor Reminder

Do not spend too much time on advanced DDS internals during Week 1.

The goal is not to make students DDS experts.

The goal is for students to understand:

```text
Linux
 ↓
Process
 ↓
Middleware
 ↓
ROS 2
 ↓
Node
 ↓
Topic / Service / Action
 ↓
ROS Graph
 ↓
Robot
```

The deeper implementation details can be introduced later when students have enough practical ROS 2 experience.

---

# 29. Week 1 Completion Criteria

Students are ready to continue when they can:

* Start a ROS 2 environment.
* Identify running ROS 2 nodes.
* List topics.
* Inspect a topic.
* Understand publisher/subscriber relationships.
* Explain services.
* Explain actions.
* List parameters.
* Read a basic ROS graph.
* Explain the software architecture of Trailobot.

---

# 30. Transition to Laboratory

The lecture should finish with:

> **Now we stop talking about ROS 2 and start using it.**

Laboratory activities:

```text
Create Workspace
      ↓
Run ROS 2 Nodes
      ↓
Inspect Nodes
      ↓
Inspect Topics
      ↓
Publish / Subscribe
      ↓
Inspect Services
      ↓
Inspect Actions
      ↓
Inspect Parameters
      ↓
Build a Small ROS 2 System
```

**Next:** Week 1 Laboratory — ROS 2 Architecture & Communication
