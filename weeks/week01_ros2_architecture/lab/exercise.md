# Week 1 Exercises

## From ROS 2 Basics to a PS4-Controlled Turtlesim System

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Total practical time:** About 8 hours, including a break and final demonstration
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Turtlesim

**Navigation:** [Lab README](README.md) · [Lab Guide](lab_guide.md) · **Exercises** · [Lecture notes](../lecture/note.md)

---

## How to Use This File

The [Lab Guide](lab_guide.md) shows you **how** to do each step. These exercises ask you to **apply and extend** it. Each exercise has:

* **Core tasks** — required for everyone. Most of them ask you to change the Lab Guide code, not only copy it.
* **Questions** — answer them in your `week01_report.md` (see [Submission](#submission)).
* **Checkpoint** — show it working before you move on.
* **Extension** — optional, for students who finish early.

**Rules for every exercise:**

1. Follow the steps in order.
2. Source ROS 2 (and your workspace) in **every** new terminal.
3. Save all code in your ROS 2 workspace and commit it to your Git repository.
4. Record any errors and explain how you solved them.
5. Take screenshots of important results.
6. Do not continue to the next exercise until the checkpoint works. Ask the instructor if you are stuck for more than 10 minutes.

---

## Exercise 1 — Prepare the ROS 2 Environment

**Time:** 20 minutes · **Lab Guide:** Section 2

### Core tasks

1. Install the lab packages (Lab Guide Section 2.1).
2. Set the `ROS_DOMAIN_ID` given by your instructor in `~/.bashrc`.
3. Create and build `~/ros2_ws` with `--symlink-install`.
4. Run the following and keep the output for your report:

```bash
echo $ROS_DISTRO
echo $ROS_DOMAIN_ID
ros2 doctor
```

### Questions

1. What is the purpose of `src/`, `build/`, `install/`, and `log/` in the workspace?
2. What does `colcon build` do?
3. Why do we source `install/setup.bash` after building?
4. Why is `ROS_DOMAIN_ID` important when 20 students work on the same network?

**Deliverable:** Screenshot showing `jazzy`, your domain ID, and a successful workspace build.

**Checkpoint:** Your workspace builds without errors and your domain ID is set.

---

## Exercise 2 — Investigate the Existing Turtlesim System

**Time:** 25 minutes · **Lab Guide:** Section 3

### Core tasks

1. Start `turtlesim_node` and `turtle_teleop_key`.
2. Use `ros2 node info`, `ros2 topic list -t`, `ros2 service list -t`, and `ros2 action list -t` to complete the table. **Do not use the Lab Guide for the answers** — find them with the CLI.

| Name                       | Kind (topic/service/action) | Type | Who provides / publishes it? | Purpose |
| -------------------------- | --------------------------- | ---- | ---------------------------- | ------- |
| `/turtle1/pose`            |                             |      |                              |         |
| `/turtle1/cmd_vel`         |                             |      |                              |         |
| `/reset`                   |                             |      |                              |         |
| `/spawn`                   |                             |      |                              |         |
| `/turtle1/rotate_absolute` |                             |      |                              |         |

3. Measure the publishing rate of `/turtle1/pose` with `ros2 topic hz`.

### Questions

1. How does Turtlesim receive motion commands, and how does it report its position?
2. At what rate is `/turtle1/pose` published?
3. Without the keyboard teleop node, can you still move the turtle? Show the command you used.

**Checkpoint:** The table is complete and you can move the turtle with a `ros2 topic pub` command on `/turtle1/cmd_vel`.

---

## Exercise 3 — Test Publishers and Subscribers from the CLI

**Time:** 30 minutes · **Lab Guide:** Section 4

### Core tasks

1. Start **two** subscribers on `/lab_message` in two terminals.
2. Publish these messages one by one with `ros2 topic pub --once -w 1`:
   * `READY`
   * `MOVING`
   * `STOPPED`
   * `ERROR`
3. Publish `MOVING` continuously at 5 Hz and verify the rate with `ros2 topic hz`.
4. Run `ros2 topic info /lab_message --verbose` and record the publisher and subscriber counts.
5. Publish once **without** any subscriber running, then start a subscriber.

### Questions

1. What happened in task 5? Why?
2. Did both subscribers in task 1 receive every message? What does this tell you about topics?
3. Why is a topic useful for continuous robot status information?

**Checkpoint:** Both subscribers receive every published message.

---

## Exercise 4 — Write a Python Publisher and Subscriber

**Time:** 45 minutes · **Lab Guide:** Section 5

### Core tasks

1. Create the `ros2_lab_basics` package and the two nodes from the Lab Guide. Build and test them.
2. **Modify the publisher** so it cycles through robot states and includes a counter:

```text
[0] READY
[1] MOVING
[2] STOPPED
[3] READY
...
```

3. **Make the publish rate a parameter** named `publish_rate_hz` (default `1.0`). Test it:

```bash
ros2 run ros2_lab_basics simple_publisher --ros-args -p publish_rate_hz:=5.0
ros2 topic hz /lab_message
```

4. **Modify the subscriber** so it counts received messages and logs a **warning** (`get_logger().warn(...)`) when the state is `STOPPED`.

### Questions

1. What is the role of `rclpy.spin()`?
2. What happens to the subscriber if you stop and restart the publisher?
3. What is the queue size `10` in `create_publisher(...)`?

**Checkpoint:** The subscriber receives the cycling states with counters, and the rate changes when you set the parameter.

**Deliverable:** Python source files, `setup.py`, and a screenshot of both nodes running.

**Extension:** Start two publishers with different node names (`--ros-args -r __node:=publisher_2`) and observe how the subscriber handles both.

---

## Exercise 5 — Use Built-in Turtlesim Services

**Time:** 25 minutes · **Lab Guide:** Section 6

### Core tasks

Using only `ros2 service call`:

1. Reset the simulator.
2. Spawn a second turtle named `turtle2` at `(2.0, 2.0)`.
3. Set `turtle1`'s pen to **green, width 5**, and `turtle2`'s pen to **blue, width 2**.
4. Move both turtles (using `ros2 topic pub` on their `cmd_vel` topics) so they draw two lines.
5. Turn `turtle1`'s pen **off**, move it, then turn it on again.
6. Clear the drawing.

Record every command you used in your report.

### Questions

1. What is a service request? What is a service response? Point to both in `ros2 interface show turtlesim/srv/Spawn`.
2. Why must `'off'` be quoted in the `SetPen` call?
3. When would a real AMR need a reset service?
4. Why is a service more suitable than a topic for a one-time reset request?

**Checkpoint:** Two turtles draw in different colors, and you can clear and reset the simulator.

---

## Exercise 6 — Create a Python Service Server and Client

**Time:** 45 minutes · **Lab Guide:** Section 7

### Core tasks

1. Create the `ros2_lab_services` package with the `add_two_ints` server and client from the Lab Guide. Build and run them.
2. Test these pairs with your client and fill in the table:

|  A |  B | Expected | Result from your client |
| -: | -: | -------: | ----------------------: |
| 10 | 20 |       30 |                         |
|  5 |  7 |       12 |                         |
| -3 |  8 |        5 |                         |

3. Call your server from the CLI with `ros2 service call` for one of the pairs.
4. Start the client **before** the server. Observe the output, then start the server.
5. **Add a second service** `/multiply_two_ints` to the **same** server node, using the same `AddTwoInts` type (the result goes in `sum`). Test it from the CLI.

### Questions

1. What did the client do in task 4? Why is `wait_for_service()` important?
2. Can one node provide more than one service?
3. Why is `AddTwoInts` not a good name for a multiply service, and what would you do in a real project?

**Checkpoint:** All three pairs return correct results, and `/multiply_two_ints` works.

**Extension:** Design a `/reset_robot` service for a real AMR. Write its request and response fields as a `.srv` definition (for example: `bool clear_map`, `---`, `bool success`, `string message`) and explain each field. You will learn to build custom interfaces later in the course.

---

## Exercise 7 — Inspect and Control a ROS 2 Action

**Time:** 30 minutes · **Lab Guide:** Section 8

### Core tasks

1. Inspect `/turtle1/rotate_absolute` and identify the goal, result, and feedback fields.
2. Send goals so the turtle faces **up**, **left**, **down**, and **right** (in that order). Record the `theta` values you used, in radians.
3. Send a long rotation and **cancel** it with `Ctrl+C` before it finishes.
4. Send a goal, and while it is running send a different goal from another terminal (**preemption**). Read the message in the `turtlesim_node` terminal.

### Questions

1. What is an action goal, feedback, and result?
2. What is the difference between cancelling and preempting a goal?
3. Why might robot navigation be implemented as an action instead of a service?

**Checkpoint:** You can send, cancel, and preempt a rotation goal and explain each part of the output.

**Extension:** Write a Python action client (`rotate_client.py`) that sends a `RotateAbsolute` goal from a command-line angle in degrees and prints the feedback. Use `rclpy.action.ActionClient`.

---

## Exercise 8 — Work with Parameters

**Time:** 20 minutes · **Lab Guide:** Section 9

### Core tasks

1. List the parameters of `/turtlesim` and read the three background color values.
2. Change the background to a color of your choice using `ros2 param set`.
3. Dump the parameters to `~/ros2_ws/src/turtle_params.yaml`.
4. Restart Turtlesim with `--params-file` and confirm your color is loaded.
5. Start your `simple_publisher` from Exercise 4 and use `ros2 param get` and `ros2 param set` on `publish_rate_hz`.

### Questions

1. What is the difference between a parameter and a topic?
2. In task 5, did `ros2 param set` change the publish rate while the node was running? Explain why or why not.

**Checkpoint:** Turtlesim starts with your custom background from a YAML file.

**Extension:** Use `add_on_set_parameters_callback` so `publish_rate_hz` can be changed while the node runs.

---

## Exercise 9 — Read PS4 Controller Data

**Time:** 25 minutes · **Lab Guide:** Section 10

### Core tasks

1. Connect the controller and confirm it is detected with `ros2 run joy joy_enumerate_devices`.
2. Start `joy_node` and inspect `/joy`.
3. Move **one control at a time** and complete this table from your own observations:

| Controller input             | Array     | Index | Value (up / left / pressed) | Value (down / right / released) |
| ---------------------------- | --------- | ----: | --------------------------- | ------------------------------- |
| Left stick up/down           | `axes`    |       |                             |                                 |
| Left stick left/right        | `axes`    |       |                             |                                 |
| Right stick up/down          | `axes`    |       |                             |                                 |
| L1 (or your "enable" button) | `buttons` |       |                             |                                 |
| R1 (or your "turbo" button)  | `buttons` |       |                             |                                 |

Your indexes may differ from another student's. **No controller?** Use the fallback in Lab Guide Section 10.3 and explain this in your report.

### Questions

1. What is the message type of `/joy`? What are its fields?
2. Why does `joy_node` not move the turtle by itself?
3. Why should we inspect actual axis indexes before writing the teleoperation node?

**Checkpoint:** You can identify the controller axes and buttons from `/joy`.

---

## Exercise 10 — Control Turtlesim Using the PS4 Controller

**Time:** 50 minutes · **Lab Guide:** Section 11

### Core tasks

1. Create the `turtle_ps4_lab` package and the `turtle_ps4_teleop` node from the Lab Guide.
2. Run it with **your** axis indexes from Exercise 9, using `--ros-args -p ...`.
3. **Add a dead-man (enable) button.** Declare a parameter `enable_button` (your L1 index). The turtle must move **only while the button is held**; when it is released, publish a zero `Twist` once.
4. **Add a turbo button.** Declare `turbo_button` (your R1 index). While it is held, multiply both scales by `2.0`.
5. Verify with `ros2 topic echo /turtle1/cmd_vel` that releasing the enable button sends zero velocity.

### Driving challenge

* Move forward and backward.
* Rotate left and right.
* Draw a circle.
* Draw a square using manual controller movements.

### Questions

1. Why is a dead-man button important on a real robot?
2. What does the turtle do if the teleop node crashes? Why? What would a real robot need instead?
3. Why are the axis indexes parameters instead of fixed numbers in the code?

**Checkpoint:** PS4 input changes `/turtle1/cmd_vel` and moves the turtle **only** while the enable button is held.

**Extension:** Add a `deadzone` parameter (e.g. `0.1`) so small stick drift is ignored.

---

## Exercise 11 — Publish and Monitor Turtle Status

**Time:** 35 minutes · **Lab Guide:** Section 12

### Core tasks

1. Create the `turtle_status_node` from the Lab Guide and run it.
2. **Add a motion state** to the status string:
   * `MOVING` when `|linear_velocity| > 0.01` or `|angular_velocity| > 0.01`
   * `STOPPED` otherwise
3. **Add a wall warning.** The Turtlesim window is about `11 × 11`. When `x` or `y` is below `1.0` or above `10.0`, include `NEAR_WALL` in the status and log a **warning** (throttled to once per second).
4. Example output:

```text
state=MOVING, x=9.84, y=5.54, theta=0.00, NEAR_WALL
```

### Questions

1. Where does the status node get its data?
2. Why does the status node publish on a separate topic instead of modifying `/turtle1/pose`?
3. What is the difference between `/turtle1/pose` and `/turtle_status`?

**Checkpoint:** `/turtle_status` shows `MOVING`/`STOPPED` correctly and warns when the turtle approaches a wall.

---

## Exercise 12 — Inspect Logs and Debug the ROS Graph

**Time:** 25 minutes · **Lab Guide:** Section 13

### Core tasks

1. Run all four nodes, then open `rqt_graph` and `rqt_console`.
2. In `rqt_graph`, identify `/joy`, `/turtle1/cmd_vel`, `/turtle1/pose`, and `/turtle_status`. Take a screenshot.
3. In `rqt_console`, find the `NEAR_WALL` warning from Exercise 11 and filter to show only warnings.
4. **Debugging scenarios.** For each scenario, describe the symptom and which command(s) helped you find the cause:

| Scenario                                                        | Symptom | Command that found the cause |
| --------------------------------------------------------------- | ------- | ---------------------------- |
| Stop the status node                                            |         |                              |
| Stop `joy_node`                                                 |         |                              |
| Start the teleop node with `-p linear_axis:=20`                 |         |                              |
| In a new terminal, run a node **without** sourcing the workspace |         |                              |

Restart everything and confirm the system works again.

**Checkpoint:** You can use `rqt_graph`, `rqt_console`, and ROS 2 CLI tools to find the cause of each problem.

---

## Exercise 13 — Launch the Complete System

**Time:** 40 minutes · **Lab Guide:** Section 14

### Core tasks

1. Create `launch/turtle_system.launch.py` that starts `turtlesim_node`, `joy_node`, `turtle_ps4_teleop`, and `turtle_status_node`.
2. **Move the controller mapping into a YAML file** `config/ps4.yaml`:

```yaml
turtle_ps4_teleop:
  ros__parameters:
    linear_axis: 1
    angular_axis: 0
    enable_button: 4
    turbo_button: 5
    linear_scale: 2.0
    angular_scale: 2.0
```

Use **your own** indexes. Load it in the launch file with:

```python
import os
from ament_index_python.packages import get_package_share_directory

config = os.path.join(
    get_package_share_directory('turtle_ps4_lab'), 'config', 'ps4.yaml'
)
# ...
Node(
    package='turtle_ps4_lab',
    executable='turtle_ps4_teleop',
    name='turtle_ps4_teleop',
    output='screen',
    parameters=[config],
),
```

3. Install both `launch/*.launch.py` and `config/*.yaml` through `data_files` in `setup.py`, and add the `exec_depend` lines to `package.xml` (Lab Guide Sections 14.3–14.4).
4. Build, source, and launch:

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select turtle_ps4_lab
source install/setup.bash
ros2 launch turtle_ps4_lab turtle_system.launch.py
```

5. Change one value in `ps4.yaml`, rebuild, relaunch, and confirm the change with `ros2 param get /turtle_ps4_teleop <name>`.

### Demonstration (show the instructor)

1. Start the system with one launch command.
2. Drive the turtle with the PS4 controller, using the enable and turbo buttons.
3. Show `/turtle1/cmd_vel`, `/turtle1/pose`, and `/turtle_status`.
4. Drive near a wall and show the warning in `rqt_console`.
5. Open `rqt_graph` and explain the data flow from controller to status.

### Questions

1. Why is a launch file better than starting four terminals by hand?
2. Why is it useful to keep the controller mapping in a YAML file instead of the launch file?

**Checkpoint:** All four nodes start together, the mapping comes from `ps4.yaml`, and the complete system works.

**Extension:** Add a launch argument `use_joy:=false` that skips `joy_node`, so the system can run without a controller.

---

## Submission

Submit a link to a Git repository (or a `.zip` if your instructor allows it) named `week01_<student_id>` with this structure:

```text
week01_<student_id>/
├── week01_report.md
├── screenshots/
│   ├── ex01_environment.png
│   ├── ex04_pub_sub.png
│   ├── ex10_ps4_turtle.png
│   ├── ex11_turtle_status.png
│   └── ex12_rqt_graph.png
└── src/
    ├── ros2_lab_basics/
    ├── ros2_lab_services/
    └── turtle_ps4_lab/
        ├── config/ps4.yaml
        ├── launch/turtle_system.launch.py
        ├── package.xml
        ├── setup.py
        └── turtle_ps4_lab/
```

Do **not** submit the `build/`, `install/`, or `log/` folders.

### Checklist

* [ ] `ros2_lab_basics` — state publisher with `publish_rate_hz` parameter, counting subscriber.
* [ ] `ros2_lab_services` — server with `/add_two_ints` and `/multiply_two_ints`, command-line client.
* [ ] `turtle_ps4_lab` — teleop node with enable and turbo buttons, status node with state and wall warning.
* [ ] `launch/turtle_system.launch.py` and `config/ps4.yaml`.
* [ ] Updated `setup.py` and `package.xml`.
* [ ] Screenshots listed above.
* [ ] `week01_report.md` containing:
  * Answers to all exercise questions and the completed tables.
  * Your PS4 controller mapping table.
  * A short explanation of topics, services, actions, parameters, and launch files, with one Turtlesim example of each.
  * A troubleshooting note describing **one problem you solved**: symptom, cause, and fix.

### Grading Rubric (Week 1 Lab)

Based on the [course laboratory rubric](../../../course/grading_rubric.md).

| Criterion               | Weight | Evidence                                                                                   |
| ----------------------- | -----: | ------------------------------------------------------------------------------------------ |
| Implementation          |   35%  | Packages build with `colcon build`; all core tasks work                                    |
| ROS 2 Understanding     |   25%  | Correct, clear answers to the questions; explanation of topics/services/actions/parameters |
| Debugging               |   15%  | Exercise 12 table and troubleshooting note show a systematic approach                      |
| Documentation           |   10%  | Clean repository, readable report, required screenshots                                    |
| Demonstration           |   15%  | Exercise 13 demonstration completed and explained confidently                              |
| **Total**               | **100%** |                                                                                          |

Extensions are not required for full marks, but may be used to recover points lost elsewhere (instructor's discretion).

---

## Suggested 8-Hour Schedule

| Time        | Activity                                           |
| ----------- | -------------------------------------------------- |
| 00:00–00:20 | Exercise 1: Environment                            |
| 00:20–00:45 | Exercise 2: Inspect Turtlesim                      |
| 00:45–01:15 | Exercise 3: CLI publisher/subscriber               |
| 01:15–02:00 | Exercise 4: Python publisher/subscriber            |
| 02:00–02:25 | Exercise 5: Built-in services                      |
| 02:25–03:10 | Exercise 6: Python service                         |
| 03:10–03:40 | Exercise 7: Actions                                |
| 03:40–03:55 | Break                                              |
| 03:55–04:15 | Exercise 8: Parameters                             |
| 04:15–04:40 | Exercise 9: PS4 input                              |
| 04:40–05:30 | Exercise 10: PS4 teleoperation                     |
| 05:30–06:05 | Exercise 11: Turtle status                         |
| 06:05–06:30 | Exercise 12: Graph, logs, and debugging            |
| 06:30–07:10 | Exercise 13: Launch file                           |
| 07:10–08:00 | Integration, troubleshooting, demonstration        |

**Instructor note:** The schedule is a target. Prioritize the core tasks of the publisher/subscriber, service server/client, PS4 teleoperation with enable button, status node, and launch integration. All **Extensions** are optional. If a student's controller cannot be made to work, accept the fake `/joy` fallback (Lab Guide Section 10.3) for Exercises 9–13.
