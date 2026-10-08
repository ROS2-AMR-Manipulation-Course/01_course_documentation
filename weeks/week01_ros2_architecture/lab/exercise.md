# Week 1 Lab Exercises

## Exercise 1 — ROS 2 Concepts

Answer briefly:

1. What is a ROS 2 node?
2. What is a topic?
3. What is a service?
4. What is an action?
5. What is a parameter?
6. What is the ROS graph?

---

## Exercise 2 — Node Investigation

Run:

```bash
ros2 node list
```

Choose one node and run:

```bash
ros2 node info <node_name>
```

Record:

* Node name
* Publishers
* Subscribers
* Services
* Parameters

---

## Exercise 3 — Topic Investigation

Choose one topic.

Run:

```bash
ros2 topic info <topic_name>
```

Then:

```bash
ros2 topic type <topic_name>
```

Answer:

1. What is the topic name?
2. What message type does it use?
3. Who publishes it?
4. Who subscribes to it?

---

## Exercise 4 — Communication Selection

For each situation, choose **Topic, Service, or Action**.

### A. LiDAR continuously sends scan data.

Answer: __________

### B. User requests a map to be saved.

Answer: __________

### C. Robot navigates to a target position.

Answer: __________

### D. IMU continuously publishes measurements.

Answer: __________

### E. Robot executes a long pick-and-place task.

Answer: __________

---

## Exercise 5 — Debugging

A subscriber is not receiving messages.

Create a debugging procedure using ROS 2 CLI commands.

Start with:

```bash
ros2 node list
```

Then decide what command you would use next.

---

## Exercise 6 — Robot Architecture

Draw a ROS 2 architecture for Trailobot containing:

* LiDAR
* Camera
* IMU
* Encoder
* SLAM
* Localization
* Nav2
* Controller
* Motors

Show the important communication relationships.

---

## Exercise 7 — Mini Challenge

Create:

```text
Robot Status Publisher
          ↓
     /robot_status
          ↓
Robot Status Subscriber
```

The publisher should periodically send:

```text
READY
MOVING
STOPPED
```

The subscriber should display the received status.

---

## Exercise 8 — Explain Your System

Prepare a short explanation:

> "My ROS 2 system contains ..."

Explain:

* Nodes
* Topics
* Message types
* Publisher
* Subscriber
* How you debugged the system

---

# Submission

Submit:

* Source code
* Terminal screenshots
* ROS graph screenshot
* Answers to exercises
* Short explanation of your system
