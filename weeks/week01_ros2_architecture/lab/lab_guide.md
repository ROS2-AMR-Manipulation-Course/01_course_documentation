# Week 1 Lab Guide — ROS 2 Architecture & Communication

## Lab Information

**Duration:** 8 hours

**Environment:** Ubuntu 24.04 · ROS 2 Jazzy

**Difficulty:** Beginner

---

# Lab Goal

In this laboratory, you will build your first basic ROS 2 system and learn how to inspect communication between ROS 2 nodes.

By the end, you should understand:

```text
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
Parameter
 ↓
ROS Graph
```

---

# Part 1 — Check the ROS 2 Environment

## 1.1 Open Terminal

Open a terminal:

```bash
Ctrl + Alt + T
```

Check the ROS 2 environment:

```bash
printenv ROS_DISTRO
```

Expected:

```text
jazzy
```

Check ROS 2:

```bash
ros2 --help
```

---

# Part 2 — Create a ROS 2 Workspace

Create the workspace:

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
```

Build the workspace:

```bash
colcon build
```

Source it:

```bash
source install/setup.bash
```

Check:

```bash
echo $ROS_DISTRO
```

---

# Part 3 — Run Your First ROS 2 Nodes

Open Terminal 1:

```bash
ros2 run demo_nodes_cpp talker
```

Open Terminal 2:

```bash
ros2 run demo_nodes_py listener
```

Observe the communication.

### Question

Which node publishes data?

Which node subscribes to data?

---

# Part 4 — Inspect Nodes

Open another terminal.

List nodes:

```bash
ros2 node list
```

You should see nodes similar to:

```text
/talker
/listener
```

Inspect a node:

```bash
ros2 node info /talker
```

Observe:

* Publishers
* Subscribers
* Services
* Action-related interfaces

---

# Part 5 — Inspect Topics

List topics:

```bash
ros2 topic list
```

Inspect the topic:

```bash
ros2 topic info /chatter
```

Read the data:

```bash
ros2 topic echo /chatter
```

Identify:

```text
Publisher:
Subscriber:
Message type:
```

---

# Part 6 — Understand the Message

Check the message type:

```bash
ros2 topic type /chatter
```

Then:

```bash
ros2 interface show std_msgs/msg/String
```

Understand that ROS 2 communication uses defined message types.

---

# Part 7 — Publish Your Own Topic

Open a terminal:

```bash
ros2 topic pub /my_topic std_msgs/msg/String "{data: 'Hello ROS 2'}"
```

In another terminal:

```bash
ros2 topic echo /my_topic
```

Observe the message.

### Challenge

Change:

```text
Hello ROS 2
```

to your own message.

---

# Part 8 — Services

List services:

```bash
ros2 service list
```

Find a service from the running system.

Inspect its type:

```bash
ros2 service type <service_name>
```

Inspect the interface:

```bash
ros2 interface show <service_type>
```

The objective is to understand:

```text
Client
  ↓
Request
  ↓
Service
  ↓
Response
  ↓
Client
```

---

# Part 9 — Actions

List actions:

```bash
ros2 action list
```

Inspect an action:

```bash
ros2 action info <action_name>
```

Check the action interface:

```bash
ros2 interface show <action_type>
```

Understand:

```text
Goal
 ↓
Execution
 ↓
Feedback
 ↓
Result
```

---

# Part 10 — Parameters

List parameters:

```bash
ros2 param list
```

Inspect a node's parameters:

```bash
ros2 param list /talker
```

Read a parameter:

```bash
ros2 param get <node_name> <parameter_name>
```

The goal is to understand that parameters configure node behavior.

---

# Part 11 — ROS Graph

Run:

```bash
rqt_graph
```

Observe:

```text
/talker
    │
    │ /chatter
    ▼
/listener
```

Identify:

* Nodes
* Topics
* Publisher
* Subscriber

---

# Part 12 — Mini ROS 2 System

Now create a small system containing:

```text
Publisher Node
      │
      │ Topic
      ▼
Subscriber Node
```

The publisher should publish a custom message.

The subscriber should display the received message.

### Required result

```text
Publisher
    │
 /my_topic
    │
    ▼
Subscriber
```

---

# Part 13 — Debugging Practice

Imagine the subscriber receives nothing.

Use the ROS 2 CLI to investigate.

Start with:

```bash
ros2 node list
```

Then:

```bash
ros2 topic list
```

Then:

```bash
ros2 topic info /my_topic
```

Then:

```bash
ros2 topic echo /my_topic
```

### Debugging mindset

```text
Is the node running?
        ↓
Does the topic exist?
        ↓
Is someone publishing?
        ↓
Is someone subscribing?
        ↓
Is the message type correct?
```

---

# Part 14 — Lab Challenge

Create a small ROS 2 communication system with:

### Node A

Publishes robot status.

Example:

```text
Robot status: READY
```

### Node B

Receives and displays the status.

### Required architecture

```text
┌──────────────┐
│ Robot Status │
│  Publisher   │
└──────┬───────┘
       │
 /robot_status
       │
       ▼
┌──────────────┐
│    Status    │
│  Subscriber  │
└──────────────┘
```

---

# Part 15 — Final Lab Check

Before finishing, verify:

* [ ] ROS 2 Jazzy works
* [ ] Workspace created
* [ ] Workspace builds successfully
* [ ] ROS 2 nodes can run
* [ ] Nodes can be inspected
* [ ] Topics can be listed
* [ ] Topic data can be viewed
* [ ] Topic data can be published
* [ ] Services can be inspected
* [ ] Actions can be inspected
* [ ] Parameters can be inspected
* [ ] ROS graph can be visualized
* [ ] Mini ROS 2 system works
* [ ] Lab challenge completed

---

# Lab Completion

You have completed Week 1 when you can explain:

> **What is a node?**

> **What is a topic?**

> **What is a service?**

> **What is an action?**

> **What is a parameter?**

> **How do I inspect a ROS 2 system?**

> **How do ROS 2 nodes communicate?**

---

# Next Lab

## Week 2 — Kinematic Modeling & Physics Simulation

You will build the **Trailobot** model using:

```text
URDF
 ↓
Xacro
 ↓
TF2
 ↓
Gazebo Harmonic
 ↓
RViz2
```
