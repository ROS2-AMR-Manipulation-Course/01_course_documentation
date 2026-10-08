# Week 1 Lab Guide — ROS 2 Architecture & Communication

## 1. Lab Overview

**Course:** ROS 2 AMR & Manipulation
**Week:** 1
**Topic:** Linux, Middleware & ROS 2 Architecture
**Lab Duration:** 8 hours
**Platform:** Ubuntu 24.04 LTS
**ROS 2:** Jazzy

This laboratory introduces students to the practical use of ROS 2.

Students will first use the ROS 2 command-line tools, then write Python ROS 2 nodes, and finally combine multiple nodes into a small robot software system.

The laboratory follows this progression:

```text
ROS 2 CLI
    ↓
Nodes
    ↓
Topics
    ↓
Turtlesim
    ↓
Python Publisher / Subscriber
    ↓
Services
    ↓
Python Service
    ↓
Parameters
    ↓
rqt_console
    ↓
Launch
    ↓
Mini ROS 2 Robot System
```

---

# 2. Learning Outcomes

By the end of this laboratory, students should be able to:

1. Create and build a ROS 2 workspace.
2. Run and inspect ROS 2 nodes.
3. Inspect ROS 2 topics and message types.
4. Publish and subscribe to ROS 2 topics.
5. Use Turtlesim to observe ROS 2 communication.
6. Create a Python ROS 2 publisher.
7. Create a Python ROS 2 subscriber.
8. Understand ROS 2 services.
9. Create a Python service server and client.
10. Inspect and use ROS 2 parameters.
11. Use `rqt_graph` to visualize ROS communication.
12. Use `rqt_console` to inspect ROS log messages.
13. Create a launch file to start multiple nodes.
14. Debug a simple ROS 2 communication system.
15. Explain the ROS 2 architecture of a small robot system.

---

# 3. Laboratory Workflow

```text
Part 1   Environment & Workspace
   ↓
Part 2   Nodes & ROS 2 CLI
   ↓
Part 3   Topics
   ↓
Part 4   Turtlesim
   ↓
Part 5   Python Publisher & Subscriber
   ↓
Part 6   Services
   ↓
Part 7   Python Service
   ↓
Part 8   Parameters & Debugging
   ↓
Part 9   Launch Files
   ↓
Part 10  Mini ROS 2 Robot System
```

---

# 4. Part 1 — Check the ROS 2 Environment

## 4.1 Source ROS 2

Open a terminal:

```bash
source /opt/ros/jazzy/setup.bash
```

Check the ROS 2 distribution:

```bash
echo $ROS_DISTRO
```

Expected:

```text
jazzy
```

Check the ROS 2 command-line interface:

```bash
ros2 --help
```

---

# 5. Create a ROS 2 Workspace

Create the workspace:

```bash
mkdir -p ~/ros2_ws/src
```

Move into the workspace:

```bash
cd ~/ros2_ws
```

Build:

```bash
colcon build
```

Source the workspace:

```bash
source install/setup.bash
```

Check the workspace:

```bash
ros2 pkg list
```

The workspace is now ready.

The normal ROS 2 development workflow is:

```text
Write Code
    ↓
Build
    ↓
Source
    ↓
Run
```

---

# 6. Part 2 — ROS 2 Nodes

ROS 2 provides example nodes that can be used to understand communication.

## 6.1 Start the Talker

Terminal 1:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_cpp talker
```

## 6.2 Start the Listener

Terminal 2:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run demo_nodes_py listener
```

The talker publishes messages and the listener receives them.

---

# 7. Inspect ROS 2 Nodes

Open another terminal.

```bash
source /opt/ros/jazzy/setup.bash
```

List nodes:

```bash
ros2 node list
```

Inspect the talker:

```bash
ros2 node info /talker
```

Students should identify:

* Node name
* Publishers
* Subscribers
* Services
* Actions

---

# 8. Part 3 — ROS 2 Topics

List topics:

```bash
ros2 topic list
```

Inspect `/chatter`:

```bash
ros2 topic info /chatter
```

Find the message type:

```bash
ros2 topic type /chatter
```

Expected:

```text
std_msgs/msg/String
```

Display the message definition:

```bash
ros2 interface show std_msgs/msg/String
```

Expected:

```text
string data
```

Read messages:

```bash
ros2 topic echo /chatter
```

---

# 9. Publish a Topic from the CLI

Create a new topic:

```bash
ros2 topic pub /my_topic std_msgs/msg/String "{data: 'Hello ROS 2'}"
```

In another terminal:

```bash
ros2 topic echo /my_topic
```

Inspect the topic:

```bash
ros2 topic info /my_topic
```

Check the publishing frequency:

```bash
ros2 topic hz /my_topic
```

---

# 10. Part 4 — Turtlesim

Turtlesim provides a simple visual robot-like environment for learning ROS 2.

## 10.1 Start Turtlesim

Terminal 1:

```bash
ros2 run turtlesim turtlesim_node
```

A turtle window should appear.

## 10.2 Start Keyboard Control

Terminal 2:

```bash
ros2 run turtlesim turtle_teleop_key
```

Use the keyboard to move the turtle.

---

# 11. Inspect the Turtlesim Topics

List topics:

```bash
ros2 topic list
```

Important topics include:

```text
/turtle1/cmd_vel
/turtle1/pose
```

Read the turtle pose:

```bash
ros2 topic echo /turtle1/pose
```

The pose contains information such as:

```text
x
y
theta
linear_velocity
angular_velocity
```

The turtle's state is therefore available through a ROS 2 topic.

---

# 12. Inspect `/turtle1/cmd_vel`

Check the message type:

```bash
ros2 topic type /turtle1/cmd_vel
```

Expected:

```text
geometry_msgs/msg/Twist
```

Display the message definition:

```bash
ros2 interface show geometry_msgs/msg/Twist
```

The important fields are:

```text
linear.x
angular.z
```

These are commonly used to control a mobile robot.

---

# 13. Part 5 — Create a Python ROS 2 Package

Create the package:

```bash
cd ~/ros2_ws/src

ros2 pkg create --build-type ament_python turtlesim_python_lab
```

The package should contain:

```text
turtlesim_python_lab/
├── package.xml
├── setup.py
├── setup.cfg
└── turtlesim_python_lab/
    └── __init__.py
```

We will add:

```text
turtle_pose_subscriber.py
turtle_cmd_publisher.py
```

---

# 14. Python Subscriber

Create:

```text
turtle_pose_subscriber.py
```

The node subscribes to:

```text
/turtle1/pose
```

Message type:

```text
turtlesim/msg/Pose
```

The subscriber should display information similar to:

```text
Turtle Position
x: 5.54
y: 5.54
theta: 0.00
```

Move the turtle with the keyboard and observe how the values change.

The communication is:

```text
turtlesim
    │
    │ /turtle1/pose
    ▼
Python Subscriber
```

---

# 15. Python Publisher

Create:

```text
turtle_cmd_publisher.py
```

The node publishes:

```text
/turtle1/cmd_vel
```

using:

```text
geometry_msgs/msg/Twist
```

The publisher should command the turtle to move.

The communication becomes:

```text
Python Publisher
       │
       │ /turtle1/cmd_vel
       ▼
    turtlesim
       │
       │ /turtle1/pose
       ▼
Python Subscriber
```

This demonstrates a complete ROS 2 communication loop.

---

# 16. Build the Python Package

From the workspace:

```bash
cd ~/ros2_ws
```

Build:

```bash
colcon build --packages-select turtlesim_python_lab
```

Source:

```bash
source install/setup.bash
```

Run the publisher:

```bash
ros2 run turtlesim_python_lab turtle_cmd_publisher
```

Run the subscriber:

```bash
ros2 run turtlesim_python_lab turtle_pose_subscriber
```

---

# 17. Inspect the Python ROS System

Check nodes:

```bash
ros2 node list
```

Check topics:

```bash
ros2 topic list
```

Inspect:

```bash
ros2 topic info /turtle1/cmd_vel
```

and:

```bash
ros2 topic info /turtle1/pose
```

Visualize the graph:

```bash
rqt_graph
```

Students should identify:

```text
Python Publisher
       │
       ▼
/turtle1/cmd_vel
       │
       ▼
turtlesim
       │
       ▼
/turtle1/pose
       │
       ▼
Python Subscriber
```

---

# 18. Part 6 — ROS 2 Services

List services:

```bash
ros2 service list
```

Inspect a service:

```bash
ros2 service type <service_name>
```

Display the interface:

```bash
ros2 interface show <service_type>
```

Explain the service model:

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

Compare it with a topic:

```text
Topic:
Publisher → Topic → Subscriber

Service:
Client → Server → Response
```

---

# 19. Part 7 — Python Service

Create a Python service server and client in the same package.

Students should implement a simple service such as:

```text
/add_two_ints
```

The request contains:

```text
a
b
```

The response contains:

```text
sum
```

The architecture is:

```text
Python Client
     │
     │ a=10, b=20
     ▼
/add_two_ints
     │
     ▼
Python Service Server
     │
     │ sum=30
     ▼
Python Client
```

Build:

```bash
cd ~/ros2_ws

colcon build --packages-select turtlesim_python_lab

source install/setup.bash
```

Run the server and client in separate terminals.

Inspect:

```bash
ros2 service list
```

---

# 20. Part 8 — Parameters

List parameters:

```bash
ros2 param list
```

Inspect a node:

```bash
ros2 param list <node_name>
```

Read a parameter:

```bash
ros2 param get <node_name> <parameter_name>
```

Discuss possible mobile robot parameters:

```text
robot_radius
max_velocity
use_sim_time
```

Parameters configure node behavior.

---

# 21. Part 9 — ROS 2 Graph

Run:

```bash
rqt_graph
```

Use it to answer:

* What nodes are running?
* Which topics connect them?
* Which node publishes?
* Which node subscribes?

Students should use `rqt_graph` throughout the lab rather than only at the end.

---

# 22. Part 10 — rqt_console

Start:

```bash
ros2 run rqt_console rqt_console
```

Use `rqt_console` to observe ROS log messages.

Students should understand the difference between:

```text
INFO
WARN
ERROR
DEBUG
```

The basic relationship is:

```text
ROS 2 Node
    │
    ├── INFO
    ├── WARN
    ├── ERROR
    └── DEBUG
          ↓
     rqt_console
```

Use this tool when debugging a ROS 2 system.

---

# 23. Debugging Workflow

When a ROS 2 system does not work, do not guess.

Use:

```text
1. Check nodes
       ↓
2. Check topics
       ↓
3. Check topic type
       ↓
4. Check publisher/subscriber
       ↓
5. Echo the topic
       ↓
6. Check ROS graph
       ↓
7. Check logs
```

Useful commands:

```bash
ros2 node list
ros2 topic list
ros2 topic info <topic>
ros2 topic type <topic>
ros2 topic echo <topic>
rqt_graph
ros2 run rqt_console rqt_console
```

---

# 24. Part 11 — Launch Multiple Nodes

Instead of starting every node separately:

```bash
ros2 run ...
ros2 run ...
ros2 run ...
```

we can use a launch file.

Create:

```text
launch/
└── turtle_system.launch.py
```

The launch file should start:

```text
turtlesim
Python publisher
Python subscriber
```

Run:

```bash
ros2 launch turtlesim_python_lab turtle_system.launch.py
```

Then verify:

```bash
ros2 node list
```

and:

```bash
rqt_graph
```

The goal is to understand:

```text
ros2 run
    ↓
Start one executable

ros2 launch
    ↓
Start a complete system
```

---

# 25. Part 12 — Mini ROS 2 Robot System

Students combine the concepts from the laboratory.

The final system should contain:

```text
                   ┌─────────────────────┐
                   │ Robot Status Node   │
                   │ Python Publisher    │
                   └──────────┬──────────┘
                              │
                       /robot_status
                              │
                              ▼
                   ┌─────────────────────┐
                   │ Status Monitor      │
                   │ Python Subscriber   │
                   └─────────────────────┘

                   ┌─────────────────────┐
                   │ Reset Service       │
                   │ Python Server       │
                   └──────────┬──────────┘
                              │
                         /reset_robot
                              │
                              ▼
                        Python Client
```

The complete system should be started using a launch file.

Students should demonstrate:

```bash
ros2 node list
ros2 topic list
ros2 service list
ros2 param list
rqt_graph
rqt_console
```

---

# 26. Connection to Trailobot

Students should now connect the concepts they learned with the course AMR.

Example:

```text
Trailobot
│
├── LiDAR
│      ↓
│    /scan
│
├── IMU
│      ↓
│    /imu/data
│
├── Odometry
│      ↓
│    /odom
│
├── Navigation
│      ↓
│    /cmd_vel
│
└── Motor Controller
```

The concepts learned in this laboratory will be used directly in:

**Week 2:** URDF/Xacro, TF2, Gazebo

**Week 3:** SLAM, localization, Nav2

**Week 4:** Manipulation and MoveIt 2

---

# 27. Final Laboratory Check

Before finishing the laboratory, students should be able to demonstrate:

* ROS 2 workspace
* ROS 2 nodes
* ROS 2 topics
* Topic message types
* Publisher/subscriber
* Turtlesim
* Python publisher
* Python subscriber
* ROS 2 service
* Python service
* Parameters
* `rqt_graph`
* `rqt_console`
* Launch file
* Mini ROS 2 system

The main learning model is:

```text
Node
 ↓
Communication Interface
 ↓
Topic / Service / Action
 ↓
Message
 ↓
Another Node
 ↓
ROS Graph
 ↓
Robot System
```
