# Week 1 Exercises

## From ROS 2 Basics to a PS4-Controlled Turtlesim System

**Total practical time:** Approximately 8 hours, including breaks and final review.

**Rules for every exercise:**

1. Follow the steps in order.
2. Run each command and observe its result.
3. Save code in your ROS 2 workspace.
4. Record any errors and explain how you solved them.
5. Take screenshots of important results.
6. Do not continue to the next exercise until the checkpoint works.

---

## Exercise 1 — Inspect the ROS 2 Environment

**Time:** 20 minutes

### Step 1: Check the environment

```bash
source /opt/ros/jazzy/setup.bash
echo $ROS_DISTRO
ros2 --help
```

### Step 2: Create and build a workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
source install/setup.bash
```

### Step 3: Answer these questions

1. What is the ROS 2 distribution?
2. What is the purpose of `src/`?
3. What does `colcon build` do?
4. Why do we source `install/setup.bash` after building?

**Deliverable:** Screenshot showing the ROS 2 distribution and successful workspace build.

**Checkpoint:** Your workspace builds without errors.

---

## Exercise 2 — Investigate the Existing Turtlesim System

**Time:** 25 minutes

### Step 1: Start Turtlesim

```bash
ros2 run turtlesim turtlesim_node
```

### Step 2: Inspect the graph

```bash
ros2 node list
ros2 topic list
ros2 service list
ros2 action list
ros2 param list /turtlesim
```

### Step 3: Inspect important topics

```bash
ros2 topic type /turtle1/pose
ros2 topic type /turtle1/cmd_vel
ros2 topic echo /turtle1/pose
```

### Step 4: Complete the table

| Name                       | Type | Purpose |
| -------------------------- | ---- | ------- |
| `/turtle1/pose`            |      |         |
| `/turtle1/cmd_vel`         |      |         |
| `/reset`                   |      |         |
| `/turtle1/rotate_absolute` |      |         |

**Checkpoint:** Explain how Turtlesim receives motion commands and reports its position.

---

## Exercise 3 — Test a Simple Publisher and Subscriber

**Time:** 30 minutes

### Step 1: Start a subscriber

```bash
ros2 topic echo /lab_message
```

### Step 2: Publish one message

In another terminal:

```bash
ros2 topic pub --once /lab_message std_msgs/msg/String "{data: 'Hello ROS 2'}"
```

### Step 3: Inspect the topic

```bash
ros2 topic info /lab_message
ros2 topic type /lab_message
```

### Step 4: Experiment

Publish these messages one by one:

* `READY`
* `MOVING`
* `STOPPED`
* `ERROR`

**Questions**

1. What happens if there is no subscriber?
2. Can more than one subscriber listen to the same topic?
3. Why is a topic useful for continuous robot status information?

**Checkpoint:** The subscriber receives each published message.

---

## Exercise 4 — Write a Python Publisher and Subscriber

**Time:** 45 minutes

Use the code and setup steps in Sections 5 of `lab_guide.md`.

### Step 1: Create the package

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python ros2_lab_basics --dependencies rclpy std_msgs
```

### Step 2: Create two Python nodes

* `simple_publisher.py`
* `simple_subscriber.py`

The publisher must publish a text message every second. The subscriber must print each received message.

### Step 3: Register the entry points

Add both executables to `setup.py`.

### Step 4: Build and run

```bash
cd ~/ros2_ws
colcon build --packages-select ros2_lab_basics
source install/setup.bash
```

Run the subscriber and publisher in separate terminals.

### Step 5: Modify the publisher

Change the message so it includes a counter:

```text
Hello ROS 2: 0
Hello ROS 2: 1
Hello ROS 2: 2
```

**Checkpoint:** Both nodes work and the subscriber receives the counter messages.

**Deliverable:** Python source files, `setup.py`, and a screenshot of successful communication.

---

## Exercise 5 — Test Built-in Turtlesim Services

**Time:** 25 minutes

### Step 1: Inspect service names and types

```bash
ros2 service list
ros2 service type /reset
ros2 service type /clear
ros2 service type /turtle1/set_pen
```

### Step 2: Call the reset service

```bash
ros2 service call /reset std_srvs/srv/Empty "{}"
```

### Step 3: Clear the drawing

```bash
ros2 service call /clear std_srvs/srv/Empty "{}"
```

### Step 4: Change the pen color

```bash
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 3, off: 0}"
```

### Step 5: Answer

1. What is a service request?
2. What is a service response?
3. When would a real AMR need a reset service?
4. Why is a service more suitable than a topic for a one-time reset request?

**Checkpoint:** You can reset Turtlesim and change its pen using service calls.

---

## Exercise 6 — Create a Python Service Server and Client

**Time:** 45 minutes

### Step 1: Create a package

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python ros2_lab_services --dependencies rclpy example_interfaces
```

### Step 2: Create the service server

Create `add_two_ints_server.py`.

The server must:

1. Provide `/add_two_ints`.
2. Receive two integer values.
3. Calculate their sum.
4. Return the result.
5. Log the request and result.

### Step 3: Create the service client

Create `add_two_ints_client.py`.

The client must:

1. Wait until the service is available.
2. Send two integers.
3. Wait for the response.
4. Print the sum.

Use the complete examples in Section 7 of `lab_guide.md`.

### Step 4: Register, build and run

Add both entry points to `setup.py`.

```bash
cd ~/ros2_ws
colcon build --packages-select ros2_lab_services
source install/setup.bash
```

Run the server in one terminal and the client in another.

### Step 5: Test different values

Test these pairs:

|  A |  B | Expected sum |
| -: | -: | -----------: |
| 10 | 20 |           30 |
|  5 |  7 |           12 |
| -3 |  8 |            5 |

**Checkpoint:** All three requests return the correct result.

**Extension:** Design a service called `/reset_robot`. Write down its request and response fields before implementing it.

---

## Exercise 7 — Inspect and Call a ROS 2 Action

**Time:** 30 minutes

### Step 1: Inspect Turtlesim actions

```bash
ros2 action list
ros2 action list -t
ros2 action info /turtle1/rotate_absolute
ros2 interface show turtlesim/action/RotateAbsolute
```

### Step 2: Send a goal

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}" --feedback
```

### Step 3: Try a second goal

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 3.14}" --feedback
```

Observe the turtle's orientation and the terminal output.

### Step 4: Answer

1. What is an action goal?
2. What information does feedback provide?
3. What is the result?
4. Why might navigation be implemented as an action instead of a service?

**Checkpoint:** You can send a rotation goal and identify feedback and result.

**Extension:** Sketch the steps for a Python action client that sends a rotate goal. Implement it only if time permits.

---

## Exercise 8 — Read PS4 Controller Data

**Time:** 30 minutes

### Step 1: Connect the controller

Connect a PS4 controller using USB or Bluetooth. Confirm that the computer detects it.

### Step 2: Start `joy_node`

```bash
ros2 run joy joy_node
```

### Step 3: Inspect `/joy`

In another terminal:

```bash
ros2 topic list
ros2 topic info /joy
ros2 topic echo /joy
```

### Step 4: Record the controller mapping

Move one control at a time and complete this table using your own observed messages.

| Controller input             | Array     | Index | Observed value |
| ---------------------------- | --------- | ----: | -------------- |
| Left stick forward/backward  | `axes`    |       |                |
| Left stick left/right        | `axes`    |       |                |
| Right stick forward/backward | `axes`    |       |                |
| Selected button              | `buttons` |       |                |

Your indexes may differ from another student's indexes.

### Step 5: Explain

1. What is the message type of `/joy`?
2. Why does `joy_node` not move the turtle by itself?
3. Why should we inspect actual axis indexes before writing the teleoperation node?

**Checkpoint:** You can identify the controller axes and buttons from `/joy`.

---

## Exercise 9 — Control Turtlesim Using the PS4 Controller

**Time:** 50 minutes

### Step 1: Create the teleoperation package

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python turtle_ps4_lab --dependencies rclpy sensor_msgs geometry_msgs std_msgs turtlesim
```

### Step 2: Create the teleoperation node

Create `turtle_ps4_teleop.py` using Section 10 of `lab_guide.md`.

The node must:

1. Subscribe to `/joy`.
2. Read the selected axes.
3. Convert the axes into linear and angular velocity.
4. Publish `geometry_msgs/msg/Twist` on `/turtle1/cmd_vel`.
5. Log that the node has started.

### Step 3: Register and build

Add this entry point to `setup.py`:

```python
'turtle_ps4_teleop = turtle_ps4_lab.turtle_ps4_teleop:main',
```

Build:

```bash
cd ~/ros2_ws
colcon build --packages-select turtle_ps4_lab
source install/setup.bash
```

### Step 4: Run the nodes

Terminal 1:

```bash
ros2 run turtlesim turtlesim_node
```

Terminal 2:

```bash
ros2 run joy joy_node
```

Terminal 3:

```bash
ros2 run turtle_ps4_lab turtle_ps4_teleop
```

### Step 5: Verify the command stream

```bash
ros2 topic echo /turtle1/cmd_vel
```

Move the sticks and observe the velocity values and turtle movement.

### Step 6: Complete the challenge

* Move forward and backward.
* Rotate left and right.
* Draw a circle.
* Draw a square using manual controller movements.

**Checkpoint:** PS4 input changes `/turtle1/cmd_vel` and moves the turtle.

**Extension:** Add a dead-man button so the turtle only moves while the selected button is held. Test button indexes with `ros2 topic echo /joy` before implementing the behavior.

---

## Exercise 10 — Publish and Monitor Turtle Status

**Time:** 35 minutes

### Step 1: Create the status node

Create `turtle_status_node.py` using Section 11 of `lab_guide.md`.

The node must:

1. Subscribe to `/turtle1/pose`.
2. Read `x`, `y`, and `theta`.
3. Read linear and angular velocity.
4. Publish a human-readable status string on `/turtle_status`.
5. Log the status.

### Step 2: Register and build

Add this entry point:

```python
'turtle_status_node = turtle_ps4_lab.turtle_status_node:main',
```

Then run:

```bash
cd ~/ros2_ws
colcon build --packages-select turtle_ps4_lab
source install/setup.bash
```

### Step 3: Start the status node

```bash
ros2 run turtle_ps4_lab turtle_status_node
```

### Step 4: Observe the status

```bash
ros2 topic echo /turtle_status
ros2 topic info /turtle_status
```

Move the turtle with the PS4 controller and observe the status updates.

### Step 5: Explain

1. Where does the status node get its data?
2. Why does the status node publish on a separate topic?
3. What is the difference between `/turtle1/pose` and `/turtle_status`?

**Checkpoint:** `/turtle_status` updates when the turtle moves.

---

## Exercise 11 — Inspect Logs and the ROS Graph

**Time:** 25 minutes

### Step 1: Open the graph

```bash
ros2 run rqt_graph rqt_graph
```

### Step 2: Open the console

```bash
ros2 run rqt_console rqt_console
```

Install the tools if they are missing:

```bash
sudo apt install ros-jazzy-rqt-graph ros-jazzy-rqt-console
```

### Step 3: Inspect the communication graph

Identify:

* `/joy`
* `/turtle1/cmd_vel`
* `/turtle1/pose`
* `/turtle_status`

The exact graph layout may differ according to which nodes are running.

### Step 4: Debug a problem

Temporarily stop the status node.

1. What happens to `/turtle_status`?
2. Does `/turtle1/pose` still exist?
3. Which command helps you identify the missing node?

Restart the status node and confirm that messages return.

**Checkpoint:** You can use `rqt_graph`, `rqt_console`, and ROS 2 CLI tools to investigate a problem.

---

## Exercise 12 — Launch the Complete System

**Time:** 45 minutes

### Step 1: Create the launch directory

```bash
cd ~/ros2_ws/src/turtle_ps4_lab
mkdir -p launch
```

Create `launch/turtle_system.launch.py` using Section 13 of `lab_guide.md`.

The launch file must start:

* `turtlesim_node`
* `joy_node`
* `turtle_ps4_teleop`
* `turtle_status_node`

### Step 2: Install the launch file

Update `setup.py` to install `launch/*.launch.py` into the package's share directory. Confirm the launch dependencies are declared in `package.xml`.

### Step 3: Build and source

```bash
cd ~/ros2_ws
colcon build --packages-select turtle_ps4_lab
source install/setup.bash
```

### Step 4: Launch everything

```bash
ros2 launch turtle_ps4_lab turtle_system.launch.py
```

### Step 5: Verify the system

```bash
ros2 node list
ros2 topic list
ros2 topic echo /turtle_status
```

Open `rqt_graph` and confirm the expected connections.

### Step 6: Demonstrate

1. Start the system with one launch command.
2. Move the turtle with the PS4 controller.
3. Show `/turtle1/cmd_vel`.
4. Show `/turtle1/pose`.
5. Show `/turtle_status`.
6. Open `rqt_graph`.

**Checkpoint:** All four nodes start together and the complete system works.

---

## Final Deliverables

Submit the following:

* [ ] `ros2_lab_basics` Python package.
* [ ] `ros2_lab_services` Python service server and client.
* [ ] `turtle_ps4_lab` teleoperation and status nodes.
* [ ] `launch/turtle_system.launch.py`.
* [ ] Updated `setup.py` and `package.xml`.
* [ ] A screenshot of the working PS4-controlled turtle.
* [ ] A screenshot of `/turtle_status`.
* [ ] A screenshot of `rqt_graph`.
* [ ] A short explanation of topics, services, actions, and launch files.
* [ ] A short troubleshooting note describing one problem you solved.

## Suggested 8-Hour Schedule

| Time        | Activity                                    |
| ----------- | ------------------------------------------- |
| 00:00–00:20 | Exercise 1: Environment                     |
| 00:20–00:45 | Exercise 2: Inspect Turtlesim               |
| 00:45–01:15 | Exercise 3: CLI publisher/subscriber        |
| 01:15–02:00 | Exercise 4: Python publisher/subscriber     |
| 02:00–02:25 | Exercise 5: Built-in services               |
| 02:25–03:10 | Exercise 6: Python service                  |
| 03:10–03:40 | Exercise 7: Action                          |
| 03:40–03:55 | Break                                       |
| 03:55–04:25 | Exercise 8: PS4 input                       |
| 04:25–05:15 | Exercise 9: PS4 teleoperation               |
| 05:15–05:50 | Exercise 10: Turtle status                  |
| 05:50–06:15 | Exercise 11: Graph and logs                 |
| 06:15–07:00 | Exercise 12: Launch file                    |
| 07:00–08:00 | Integration, troubleshooting, demonstration |

**Instructor note:** The schedule is a target, not a requirement that every student finish every extension. Prioritize the basic publisher/subscriber, service client/server, PS4 teleoperation, status publisher, and launch integration. The Python action client and dead-man button can be optional extensions if time is limited.
