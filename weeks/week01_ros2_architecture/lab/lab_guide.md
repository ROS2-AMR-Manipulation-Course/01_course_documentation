# Week 1 Lab Guide

## ROS 2 Communication, Python Nodes, PS4 Teleoperation and Launch Files

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 1
> **Estimated duration:** 8 hours
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Turtlesim
> **Main objective:** Build a small ROS 2 system step by step, starting with simple communication and finishing with PS4-controlled Turtlesim.

**Navigation:** [Lab README](README.md) · **Lab Guide** · [Exercises](exercise.md) · [Lecture notes](../lecture/note.md)

---

## 1. Laboratory Overview

In this lab, you will learn how ROS 2 nodes communicate using topics, services, actions, parameters, and launch files.

You will first test existing ROS 2 tools, then create small Python programs. Finally, you will integrate the nodes into one system that uses a PS4 controller to move a turtle and publishes its status.

### 1.1 Concept summary

| Mechanism     | Pattern                                | Use it when…                                         | Turtlesim example           |
| ------------- | -------------------------------------- | ---------------------------------------------------- | --------------------------- |
| **Topic**     | Publish / subscribe, continuous stream | Data is produced repeatedly (sensors, commands)      | `/turtle1/pose`             |
| **Service**   | Request / response, short call         | You need a quick, one-time answer or operation       | `/reset`, `/turtle1/set_pen` |
| **Action**    | Goal / feedback / result, cancellable  | The task takes time and you want progress or cancel  | `/turtle1/rotate_absolute`  |
| **Parameter** | Named configuration value of a node    | You want to change behavior without editing code     | `background_r`              |
| **Launch**    | Start and configure many nodes at once | The system has more than one or two nodes            | `turtle_system.launch.py`   |

### 1.2 Final system architecture

```text
PS4 Controller
      |
      v
   joy_node
      |
      | /joy                (sensor_msgs/msg/Joy)
      v
turtle_ps4_teleop
      |
      | /turtle1/cmd_vel    (geometry_msgs/msg/Twist)
      v
  turtlesim_node
      |
      | /turtle1/pose       (turtlesim/msg/Pose)
      v
turtle_status_node
      |
      | /turtle_status      (std_msgs/msg/String)
      v
ros2 topic echo / rqt

Additional communication:
- Service:   /reset, /clear, /turtle1/set_pen, /add_two_ints
- Action:    /turtle1/rotate_absolute
- Parameter: /turtlesim background_*, /turtle_ps4_teleop axis mapping
- Tools:     rqt_graph, rqt_console
- Startup:   ros2 launch
```

---

## 2. Set Up the ROS 2 Environment

### 2.1 Install the packages used in this lab

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

### 2.2 Isolate your ROS 2 network (important in a classroom)

ROS 2 nodes automatically discover every other ROS 2 node on the same network. In a lab room, this means you may see — and accidentally control — **another student's turtle**.

Choose **one** of the following and add it to `~/.bashrc`:

```bash
# Option A (recommended): a unique domain ID. Use the number given by your instructor (1–100).
echo "export ROS_DOMAIN_ID=<your_number>" >> ~/.bashrc

# Option B: only discover nodes running on your own computer.
echo "export ROS_AUTOMATIC_DISCOVERY_RANGE=LOCALHOST" >> ~/.bashrc
```

Then open a new terminal and check:

```bash
echo $ROS_DOMAIN_ID
echo $ROS_AUTOMATIC_DISCOVERY_RANGE
```

### 2.3 Source ROS 2 in every terminal

> **Every new terminal must be sourced.** Most "command not found" or "package not found" errors in this lab come from a terminal that was not sourced.

```bash
source /opt/ros/jazzy/setup.bash
```

To do this automatically, add it to `~/.bashrc` once:

```bash
echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
```

Check the environment:

```bash
echo $ROS_DISTRO
ros2 --help
ros2 doctor
```

Expected:

```text
jazzy
```

### 2.4 Create and build a workspace

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build --symlink-install
source install/setup.bash
```

`--symlink-install` links your Python files instead of copying them, so most Python edits do not need a rebuild. You must still rebuild after changing `setup.py`, `package.xml`, or adding new files.

> **After the workspace is built,** every terminal that runs *your own* packages must also run:
>
> ```bash
> source ~/ros2_ws/install/setup.bash
> ```

**Checkpoint:** `echo $ROS_DISTRO` prints `jazzy`, and the empty workspace builds without errors.

---

## 3. Start Turtlesim and Inspect the ROS Graph

Terminal 1:

```bash
ros2 run turtlesim turtlesim_node
```

Terminal 2:

```bash
ros2 run turtlesim turtle_teleop_key
```

Click inside Terminal 2 and use the arrow keys to move the turtle.

Terminal 3 — inspect the graph:

```bash
ros2 node list
ros2 node info /turtlesim
ros2 topic list -t
ros2 service list
ros2 action list
ros2 param list /turtlesim
```

Expected `ros2 node list` output:

```text
/teleop_turtle
/turtlesim
```

Inspect the important topics and their message types:

```bash
ros2 topic type /turtle1/pose
ros2 topic type /turtle1/cmd_vel
ros2 interface show turtlesim/msg/Pose
ros2 interface show geometry_msgs/msg/Twist
```

Monitor the turtle's position and how often it is published:

```bash
ros2 topic echo /turtle1/pose
ros2 topic hz /turtle1/pose
```

Example `echo` output:

```text
x: 5.544444561004639
y: 5.544444561004639
theta: 0.0
linear_velocity: 0.0
angular_velocity: 0.0
---
```

Stop each command with `Ctrl+C`.

**Checkpoint:** Explain which node publishes `/turtle1/pose` and which node subscribes to `/turtle1/cmd_vel`.

---

## 4. Test a Publisher and Subscriber Using CLI Tools

Before writing code, test ROS 2 communication from the terminal.

### 4.1 Start a subscriber

Terminal 1:

```bash
ros2 topic echo /lab_message std_msgs/msg/String
```

Giving the message type lets `echo` start before any publisher exists.

### 4.2 Publish one message

Terminal 2:

```bash
ros2 topic pub --once -w 1 /lab_message std_msgs/msg/String "{data: 'Hello ROS 2'}"
```

`-w 1` waits until one subscriber is connected before publishing, so the single message is not lost.

Expected output in Terminal 1:

```text
data: Hello ROS 2
---
```

### 4.3 Publish continuously

```bash
ros2 topic pub -r 2 /lab_message std_msgs/msg/String "{data: 'Hello at 2 Hz'}"
```

`-r 2` publishes at 2 Hz. Stop it with `Ctrl+C`.

### 4.4 Inspect the topic

While the publisher is running:

```bash
ros2 topic info /lab_message --verbose
ros2 topic hz /lab_message
ros2 interface show std_msgs/msg/String
```

**Checkpoint:** Explain the publisher, subscriber, topic name, and message type.

---

## 5. Create a Simple Python Publisher and Subscriber

Create a package:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python ros2_lab_basics --dependencies rclpy std_msgs
```

The generated package looks like this. Note that there are **two** folders named `ros2_lab_basics` — your Python files go in the **inner** one:

```text
~/ros2_ws/src/ros2_lab_basics/
├── package.xml
├── setup.py
├── setup.cfg
├── resource/
├── test/
└── ros2_lab_basics/          <- Python nodes go here
    └── __init__.py
```

### 5.1 Create the publisher

Create `~/ros2_ws/src/ros2_lab_basics/ros2_lab_basics/simple_publisher.py`:

```python
import rclpy
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node
from std_msgs.msg import String


class SimplePublisher(Node):
    def __init__(self):
        super().__init__('simple_publisher')
        self.publisher = self.create_publisher(String, '/lab_message', 10)
        self.timer = self.create_timer(1.0, self.publish_message)

    def publish_message(self):
        msg = String()
        msg.data = 'Hello ROS 2'
        self.publisher.publish(msg)
        self.get_logger().info(f'Published: {msg.data}')


def main(args=None):
    rclpy.init(args=args)
    node = SimplePublisher()
    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        rclpy.try_shutdown()


if __name__ == '__main__':
    main()
```

> **Why `try_shutdown()`?** In ROS 2 Jazzy, pressing `Ctrl+C` already shuts down rclpy. Calling `rclpy.shutdown()` a second time raises an error; `rclpy.try_shutdown()` is safe in both cases.

### 5.2 Create the subscriber

Create `~/ros2_ws/src/ros2_lab_basics/ros2_lab_basics/simple_subscriber.py`:

```python
import rclpy
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node
from std_msgs.msg import String


class SimpleSubscriber(Node):
    def __init__(self):
        super().__init__('simple_subscriber')
        self.subscription = self.create_subscription(
            String, '/lab_message', self.message_callback, 10
        )

    def message_callback(self, msg):
        self.get_logger().info(f'Received: {msg.data}')


def main(args=None):
    rclpy.init(args=args)
    node = SimpleSubscriber()
    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        rclpy.try_shutdown()


if __name__ == '__main__':
    main()
```

### 5.3 Register the commands

Edit `~/ros2_ws/src/ros2_lab_basics/setup.py`. Change only the `entry_points` section; keep everything else that `ros2 pkg create` generated:

```python
entry_points={
    'console_scripts': [
        'simple_publisher = ros2_lab_basics.simple_publisher:main',
        'simple_subscriber = ros2_lab_basics.simple_subscriber:main',
    ],
},
```

The format is `command_name = package_folder.file_name:function`.

### 5.4 Build and test

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select ros2_lab_basics
source install/setup.bash
```

Terminal 1:

```bash
source ~/ros2_ws/install/setup.bash
ros2 run ros2_lab_basics simple_subscriber
```

Terminal 2:

```bash
source ~/ros2_ws/install/setup.bash
ros2 run ros2_lab_basics simple_publisher
```

Expected output in Terminal 1:

```text
[INFO] [...] [simple_subscriber]: Received: Hello ROS 2
[INFO] [...] [simple_subscriber]: Received: Hello ROS 2
```

**Checkpoint:** Both nodes run and a message arrives every second.

---

## 6. Test Services

ROS 2 services use request/response communication: a client sends one request and waits for one response.

### 6.1 Inspect Turtlesim services

Start Turtlesim if it is not already running.

```bash
ros2 service list -t
ros2 service type /reset
ros2 service type /clear
ros2 service type /turtle1/set_pen
```

Inspect service definitions. The line `---` separates the **request** (above) from the **response** (below):

```bash
ros2 interface show std_srvs/srv/Empty
ros2 interface show turtlesim/srv/SetPen
```

### 6.2 Call built-in services

Reset the turtle:

```bash
ros2 service call /reset std_srvs/srv/Empty "{}"
```

Clear the drawing:

```bash
ros2 service call /clear std_srvs/srv/Empty "{}"
```

Change the turtle pen:

```bash
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 3, 'off': 0}"
```

> **Why is `'off'` quoted?** The command-line arguments are YAML. In YAML, a bare `off` means the boolean `false`, so the field name would be lost. Quoting it keeps it as the text `off`.

Spawn a second turtle:

```bash
ros2 interface show turtlesim/srv/Spawn
ros2 service call /spawn turtlesim/srv/Spawn "{x: 2.0, y: 2.0, theta: 0.0, name: 'turtle2'}"
ros2 topic list
```

Notice that new `/turtle2/...` topics appear.

**Checkpoint:** Explain why a service is suitable for requesting a reset.

---

## 7. Create a Python Service Server and Client

Create another package:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python ros2_lab_services --dependencies rclpy example_interfaces
ros2 interface show example_interfaces/srv/AddTwoInts
```

### 7.1 Create the service server

Create `~/ros2_ws/src/ros2_lab_services/ros2_lab_services/add_two_ints_server.py`:

```python
import rclpy
from example_interfaces.srv import AddTwoInts
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node


class AddTwoIntsServer(Node):
    def __init__(self):
        super().__init__('add_two_ints_server')
        self.server = self.create_service(
            AddTwoInts, '/add_two_ints', self.add_callback
        )
        self.get_logger().info('Service /add_two_ints is ready')

    def add_callback(self, request, response):
        response.sum = request.a + request.b
        self.get_logger().info(f'{request.a} + {request.b} = {response.sum}')
        return response


def main(args=None):
    rclpy.init(args=args)
    node = AddTwoIntsServer()
    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        rclpy.try_shutdown()


if __name__ == '__main__':
    main()
```

### 7.2 Create the service client

The client reads the two numbers from the command line, so you can test different values without editing code.

Create `~/ros2_ws/src/ros2_lab_services/ros2_lab_services/add_two_ints_client.py`:

```python
import sys

import rclpy
from example_interfaces.srv import AddTwoInts
from rclpy.node import Node
from rclpy.utilities import remove_ros_args


class AddTwoIntsClient(Node):
    def __init__(self):
        super().__init__('add_two_ints_client')
        self.client = self.create_client(AddTwoInts, '/add_two_ints')

    def send_request(self, a, b):
        while not self.client.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('Waiting for service /add_two_ints...')

        request = AddTwoInts.Request()
        request.a = a
        request.b = b
        future = self.client.call_async(request)
        rclpy.spin_until_future_complete(self, future)

        if future.result() is not None:
            self.get_logger().info(f'Result: {a} + {b} = {future.result().sum}')
        else:
            self.get_logger().error('Service call failed')


def main(args=None):
    rclpy.init(args=args)
    argv = remove_ros_args(sys.argv)
    if len(argv) != 3:
        print('Usage: ros2 run ros2_lab_services add_two_ints_client <a> <b>')
        rclpy.try_shutdown()
        return

    node = AddTwoIntsClient()
    try:
        node.send_request(int(argv[1]), int(argv[2]))
    except KeyboardInterrupt:
        pass
    finally:
        node.destroy_node()
        rclpy.try_shutdown()


if __name__ == '__main__':
    main()
```

### 7.3 Register, build and run

In `~/ros2_ws/src/ros2_lab_services/setup.py`:

```python
entry_points={
    'console_scripts': [
        'add_two_ints_server = ros2_lab_services.add_two_ints_server:main',
        'add_two_ints_client = ros2_lab_services.add_two_ints_client:main',
    ],
},
```

Build:

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select ros2_lab_services
source install/setup.bash
```

Terminal 1 — server:

```bash
ros2 run ros2_lab_services add_two_ints_server
```

Terminal 2 — client:

```bash
ros2 run ros2_lab_services add_two_ints_client 10 20
```

Expected output:

```text
[INFO] [...] [add_two_ints_client]: Result: 10 + 20 = 30
```

You can also call your own service from the CLI, exactly like a built-in one:

```bash
ros2 service call /add_two_ints example_interfaces/srv/AddTwoInts "{a: 5, b: 7}"
```

**Checkpoint:** The client sends two numbers and receives their sum from the server.

---

## 8. Inspect and Test an Action

Actions are for longer tasks. The client sends a **goal**, receives **feedback** while the task runs, and finally receives a **result**. A goal can also be **cancelled**.

Turtlesim provides `/turtle1/rotate_absolute`.

```bash
ros2 action list -t
ros2 action info /turtle1/rotate_absolute
ros2 interface show turtlesim/action/RotateAbsolute
```

The interface has three parts separated by `---`: **goal**, **result**, **feedback**.

```text
# The desired heading in radians
float32 theta
---
# The angular displacement in radians to the starting position
float32 delta
---
# The remaining rotation in radians
float32 remaining
```

### 8.1 Send a goal

`theta` is in **radians** (`1.57` ≈ 90°, `3.14` ≈ 180°, `0.0` = facing right).

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}" --feedback
```

Watch the `remaining` feedback decrease, then the final `delta` result.

### 8.2 Cancel a goal

Send a large rotation and press `Ctrl+C` while it is still turning:

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: -3.0}" --feedback
```

The CLI cancels the goal and the turtle stops rotating before reaching the target.

### 8.3 Preempt a goal

Start a goal in one terminal and, while it is running, send a different goal from a second terminal. Observe the message in the `turtlesim_node` terminal: the first goal is aborted and the new goal is executed.

**Checkpoint:** Identify the goal, feedback, and result, and explain the difference between cancelling and preempting a goal.

---

## 9. Inspect and Change Parameters

Parameters are named configuration values that belong to a node. They let you change behavior without editing code.

With `turtlesim_node` running:

```bash
ros2 param list /turtlesim
ros2 param get /turtlesim background_r
ros2 param describe /turtlesim background_r
```

Change the background color:

```bash
ros2 param set /turtlesim background_r 150
ros2 param set /turtlesim background_g 50
ros2 param set /turtlesim background_b 50
```

If the color does not change immediately, call `ros2 service call /clear std_srvs/srv/Empty "{}"`.

Save all parameters of the node to a YAML file and load them on the next start:

```bash
ros2 param dump /turtlesim > ~/turtlesim_params.yaml
cat ~/turtlesim_params.yaml
```

Stop Turtlesim, then restart it with the saved file:

```bash
ros2 run turtlesim turtlesim_node --ros-args --params-file ~/turtlesim_params.yaml
```

You can also set a single parameter at startup:

```bash
ros2 run turtlesim turtlesim_node --ros-args -p background_b:=200
```

You will use parameters again in Section 11, where the PS4 axis mapping is configurable.

**Checkpoint:** You can read, change, save, and load a node's parameters.

---

## 10. Read PS4 Controller Input with `joy_node`

Connect your PS4 controller through USB or Bluetooth.

> **Using a virtual machine?** The controller must be passed through to the VM (e.g. VirtualBox: *Devices → USB → Sony Wireless Controller*). If you cannot get a controller working, use the **fallback** in Section 10.3 so you can continue the lab.

### 10.1 Confirm the controller is detected

```bash
ros2 run joy joy_enumerate_devices
```

Expected output lists your controller, for example:

```text
Joystick Device ID : Joystick Device Name
-----------------------------------------
                 0 : PS4 Controller
```

### 10.2 Start the joystick node and inspect `/joy`

Terminal 1:

```bash
ros2 run joy joy_node
```

Terminal 2:

```bash
ros2 topic list
ros2 topic info /joy
ros2 interface show sensor_msgs/msg/Joy
ros2 topic echo /joy
```

Move each stick and press each button. Observe changes to the `axes` (floats from `-1.0` to `1.0`) and `buttons` (`0` or `1`) arrays.

**Do not assume the axis indexes.** Move **one** control at a time and record which index changes and its sign. Controller mappings can differ between USB, Bluetooth, and driver versions.

| Controller input      | Array     | Index | Value when pushed forward/left/pressed |
| --------------------- | --------- | ----: | -------------------------------------- |
| Left stick up/down    | `axes`    |       |                                        |
| Left stick left/right | `axes`    |       |                                        |
| Button for "enable"   | `buttons` |       |                                        |

If `/joy` is missing, check the controller connection and the `joy_node` terminal output first.

### 10.3 Fallback: simulate a controller

If you have no working controller, publish fake `/joy` messages. This message pushes the "left stick" forward on axis `1` and holds button `4`:

```bash
ros2 topic pub -r 20 /joy sensor_msgs/msg/Joy \
  "{axes: [0.0, 0.5, 0.0, 0.0, 0.0, 0.0], buttons: [0, 0, 0, 0, 1, 0, 0, 0]}"
```

Change the values to simulate turning (`axes[0]`) or releasing a button. When using the fallback, use axis `1`/`0` and button `4` in the exercises.

**Checkpoint:** You can identify the relevant axes and a button from actual `/joy` messages.

---

## 11. Create the PS4-to-Turtlesim Teleoperation Node

Create a package for the rest of the lab:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python turtle_ps4_lab \
  --dependencies rclpy sensor_msgs geometry_msgs std_msgs turtlesim
```

Create `~/ros2_ws/src/turtle_ps4_lab/turtle_ps4_lab/turtle_ps4_teleop.py`:

```python
import rclpy
from geometry_msgs.msg import Twist
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node
from sensor_msgs.msg import Joy


class TurtlePS4Teleop(Node):
    def __init__(self):
        super().__init__('turtle_ps4_teleop')

        # Update these indexes after inspecting your own /joy output.
        self.declare_parameter('linear_axis', 1)
        self.declare_parameter('angular_axis', 0)
        self.declare_parameter('linear_scale', 2.0)
        self.declare_parameter('angular_scale', 2.0)

        # Parameters are read once here. Changing them with `ros2 param set`
        # while the node is running has no effect on these variables.
        self.linear_axis = self.get_parameter('linear_axis').value
        self.angular_axis = self.get_parameter('angular_axis').value
        self.linear_scale = self.get_parameter('linear_scale').value
        self.angular_scale = self.get_parameter('angular_scale').value

        self.publisher = self.create_publisher(Twist, '/turtle1/cmd_vel', 10)
        self.subscription = self.create_subscription(
            Joy, '/joy', self.joy_callback, 10
        )

        self.get_logger().info(
            f'PS4 teleoperation started: linear_axis={self.linear_axis}, '
            f'angular_axis={self.angular_axis}'
        )

    def joy_callback(self, msg):
        if max(self.linear_axis, self.angular_axis) >= len(msg.axes):
            self.get_logger().error(
                f'Axis index out of range: /joy has {len(msg.axes)} axes'
            )
            return

        cmd = Twist()
        cmd.linear.x = float(msg.axes[self.linear_axis]) * self.linear_scale
        cmd.angular.z = float(msg.axes[self.angular_axis]) * self.angular_scale
        self.publisher.publish(cmd)


def main(args=None):
    rclpy.init(args=args)
    node = TurtlePS4Teleop()
    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        rclpy.try_shutdown()


if __name__ == '__main__':
    main()
```

> **What happens when the node stops?** Turtlesim stops the turtle automatically if it receives no velocity command for about one second. A real robot must have a similar safety behavior (a *command timeout*). You will add a dead-man button in the exercises.

Register the executable in `~/ros2_ws/src/turtle_ps4_lab/setup.py`:

```python
entry_points={
    'console_scripts': [
        'turtle_ps4_teleop = turtle_ps4_lab.turtle_ps4_teleop:main',
    ],
},
```

Build and source:

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select turtle_ps4_lab
source install/setup.bash
```

Run the nodes in three separate terminals (source the workspace in each):

```bash
ros2 run turtlesim turtlesim_node
```

```bash
ros2 run joy joy_node
```

```bash
ros2 run turtle_ps4_lab turtle_ps4_teleop
```

Move the stick and observe the turtle. Verify the command stream:

```bash
ros2 topic echo /turtle1/cmd_vel
```

If the directions are wrong, restart the node with the indexes you recorded in Section 10. A negative scale inverts the direction:

```bash
ros2 run turtle_ps4_lab turtle_ps4_teleop --ros-args \
  -p linear_axis:=1 -p angular_axis:=0 -p angular_scale:=-2.0
```

**Safety note:** Test one axis at a time and keep the controller centered before changing mappings. On a real robot, start with small scales.

**Checkpoint:** Moving the stick changes `/turtle1/cmd_vel` and moves the turtle.

---

## 12. Publish Turtle Status

The Turtlesim node publishes `/turtle1/pose`. Create a status node that subscribes to the pose and publishes a human-readable status string.

Create `~/ros2_ws/src/turtle_ps4_lab/turtle_ps4_lab/turtle_status_node.py`:

```python
import rclpy
from rclpy.executors import ExternalShutdownException
from rclpy.node import Node
from std_msgs.msg import String
from turtlesim.msg import Pose


class TurtleStatusNode(Node):
    def __init__(self):
        super().__init__('turtle_status_node')
        self.publisher = self.create_publisher(String, '/turtle_status', 10)
        self.subscription = self.create_subscription(
            Pose, '/turtle1/pose', self.pose_callback, 10
        )
        self.get_logger().info('Turtle status node started')

    def pose_callback(self, pose):
        msg = String()
        msg.data = (
            f'x={pose.x:.2f}, y={pose.y:.2f}, '
            f'theta={pose.theta:.2f}, '
            f'linear_velocity={pose.linear_velocity:.2f}, '
            f'angular_velocity={pose.angular_velocity:.2f}'
        )
        self.publisher.publish(msg)
        # Pose arrives about 60 times per second; throttle the log output.
        self.get_logger().info(msg.data, throttle_duration_sec=1.0)


def main(args=None):
    rclpy.init(args=args)
    node = TurtleStatusNode()
    try:
        rclpy.spin(node)
    except (KeyboardInterrupt, ExternalShutdownException):
        pass
    finally:
        node.destroy_node()
        rclpy.try_shutdown()


if __name__ == '__main__':
    main()
```

Add a second console entry, so `entry_points` becomes:

```python
entry_points={
    'console_scripts': [
        'turtle_ps4_teleop = turtle_ps4_lab.turtle_ps4_teleop:main',
        'turtle_status_node = turtle_ps4_lab.turtle_status_node:main',
    ],
},
```

Rebuild (required because `setup.py` changed):

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select turtle_ps4_lab
source install/setup.bash
```

Run the status node:

```bash
ros2 run turtle_ps4_lab turtle_status_node
```

Observe its output:

```bash
ros2 topic echo /turtle_status
ros2 topic info /turtle_status
ros2 topic hz /turtle_status
```

**Checkpoint:** The status updates as the turtle moves.

---

## 13. Inspect Logs and the Graph

Open the graph tool:

```bash
ros2 run rqt_graph rqt_graph
```

In `rqt_graph`, select **Nodes/Topics (all)** and press the refresh button. You should see:

```text
/joy_node --/joy--> /turtle_ps4_teleop --/turtle1/cmd_vel--> /turtlesim --/turtle1/pose--> /turtle_status_node
```

Open the logging tool:

```bash
ros2 run rqt_console rqt_console
```

Observe messages from the Python nodes. They use `get_logger().info(...)` and `get_logger().error(...)`, so the messages appear with their severity and node name. Try filtering by node or severity.

You can also change a node's log level at startup:

```bash
ros2 run turtle_ps4_lab turtle_status_node --ros-args --log-level debug
```

Useful inspection commands:

```bash
ros2 node list
ros2 node info /turtle_ps4_teleop
ros2 topic info /joy
ros2 topic info /turtle1/cmd_vel
ros2 topic info /turtle1/pose
ros2 topic info /turtle_status
```

**Checkpoint:** You can find every node and topic of the system in both `rqt_graph` and the CLI.

---

## 14. Create a Launch File

The goal is to start Turtlesim, the joystick node, the teleoperation node, and the status node from one command.

### 14.1 Final package layout

```text
~/ros2_ws/src/turtle_ps4_lab/
├── launch/
│   └── turtle_system.launch.py     <- new
├── package.xml                      <- updated
├── resource/
│   └── turtle_ps4_lab
├── setup.cfg
├── setup.py                         <- updated
├── test/
└── turtle_ps4_lab/
    ├── __init__.py
    ├── turtle_ps4_teleop.py
    └── turtle_status_node.py
```

```bash
mkdir -p ~/ros2_ws/src/turtle_ps4_lab/launch
```

### 14.2 Create the launch file

Create `~/ros2_ws/src/turtle_ps4_lab/launch/turtle_system.launch.py`:

```python
from launch import LaunchDescription
from launch_ros.actions import Node


def generate_launch_description():
    return LaunchDescription([
        Node(
            package='turtlesim',
            executable='turtlesim_node',
            name='turtlesim',
            output='screen',
        ),
        Node(
            package='joy',
            executable='joy_node',
            name='joy_node',
            output='screen',
        ),
        Node(
            package='turtle_ps4_lab',
            executable='turtle_ps4_teleop',
            name='turtle_ps4_teleop',
            output='screen',
            parameters=[{
                'linear_axis': 1,
                'angular_axis': 0,
                'linear_scale': 2.0,
                'angular_scale': 2.0,
            }],
        ),
        Node(
            package='turtle_ps4_lab',
            executable='turtle_status_node',
            name='turtle_status_node',
            output='screen',
        ),
    ])
```

**Important:** The axis indexes in this launch file are examples. Replace them with the indexes you recorded in Section 10.

### 14.3 Install the launch file — complete `setup.py`

Your final `~/ros2_ws/src/turtle_ps4_lab/setup.py` should look like this. Keep the `maintainer`, `description`, and `license` values that were generated for you:

```python
import os
from glob import glob

from setuptools import find_packages, setup

package_name = 'turtle_ps4_lab'

setup(
    name=package_name,
    version='0.0.0',
    packages=find_packages(exclude=['test']),
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        (os.path.join('share', package_name, 'launch'),
            glob('launch/*.launch.py')),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='your_name',
    maintainer_email='you@example.com',
    description='Week 1 lab: PS4-controlled Turtlesim',
    license='Apache-2.0',
    entry_points={
        'console_scripts': [
            'turtle_ps4_teleop = turtle_ps4_lab.turtle_ps4_teleop:main',
            'turtle_status_node = turtle_ps4_lab.turtle_status_node:main',
        ],
    },
)
```

### 14.4 Declare dependencies — `package.xml`

`ros2 pkg create` already added `<depend>` lines for the dependencies you passed. Add the packages that are only needed at runtime by the launch file, below the existing `<depend>` lines:

```xml
<depend>rclpy</depend>
<depend>sensor_msgs</depend>
<depend>geometry_msgs</depend>
<depend>std_msgs</depend>
<depend>turtlesim</depend>

<exec_depend>joy</exec_depend>
<exec_depend>launch</exec_depend>
<exec_depend>launch_ros</exec_depend>
```

### 14.5 Build and launch

```bash
cd ~/ros2_ws
colcon build --symlink-install --packages-select turtle_ps4_lab
source install/setup.bash
ros2 launch turtle_ps4_lab turtle_system.launch.py
```

Stop any nodes you started manually first, otherwise you will have two copies of the same node.

Verify in another terminal:

```bash
ros2 node list
ros2 topic list
ros2 topic echo /turtle_status
```

Expected `ros2 node list`:

```text
/joy_node
/turtle_ps4_teleop
/turtle_status_node
/turtlesim
```

Use `rqt_graph` to inspect the integrated system.

**Checkpoint:** One launch command starts all four nodes, and the PS4 controller moves the turtle while `/turtle_status` updates.

---

## 15. Troubleshooting

| Problem                                          | What to check                                                                                 |
| ------------------------------------------------ | --------------------------------------------------------------------------------------------- |
| `ROS_DISTRO` is empty / `ros2: command not found` | Run `source /opt/ros/jazzy/setup.bash` in this terminal                                       |
| `Package '...' not found`                        | Rebuild, then `source ~/ros2_ws/install/setup.bash` in this terminal                          |
| `No executable found`                            | Check `entry_points` in `setup.py`, then rebuild                                              |
| I see nodes/topics I did not start               | Another student is on the same network: set `ROS_DOMAIN_ID` (Section 2.2)                    |
| My turtle moves by itself                        | Same as above — someone else is publishing to your `/turtle1/cmd_vel`                         |
| `ros2 topic pub --once` message not received     | Add `-w 1`, or start the subscriber first                                                     |
| `set_pen` call fails to parse                    | Quote the field name: `'off': 0`                                                              |
| `/joy` does not appear                           | Run `ros2 run joy joy_enumerate_devices`; check USB/Bluetooth and VM passthrough              |
| Turtle does not move                             | `ros2 topic echo /joy`, then `ros2 topic echo /turtle1/cmd_vel` — find where the data stops   |
| Turtle moves in the wrong direction              | Verify axis indexes and signs; use a negative scale to invert                                 |
| `Axis index out of range` error                  | Your controller has fewer axes than the configured index — update the parameters              |
| Status topic is missing                          | Check the status node is running and `/turtle1/pose` is published                             |
| Launch file cannot be found                      | Check the `data_files` launch entry in `setup.py`, rebuild, and re-source                     |
| `rqt_graph` is empty                             | Press refresh and select *Nodes/Topics (all)*                                                 |
| `rqt_console` is empty                           | Confirm the nodes are running; start `rqt_console` before triggering log messages             |
| Python edit has no effect                        | Build with `--symlink-install`, or rebuild after each edit                                    |

---

## 16. Final Checklist

* [ ] My terminals are sourced and my network is isolated with `ROS_DOMAIN_ID`.
* [ ] I can inspect nodes, topics, services, actions, and parameters.
* [ ] I tested a CLI publisher and subscriber.
* [ ] I wrote a Python publisher and subscriber.
* [ ] I called built-in Turtlesim services.
* [ ] I created and tested a Python service server and client.
* [ ] I sent, cancelled, and preempted a Turtlesim action goal.
* [ ] I read, changed, saved, and loaded node parameters.
* [ ] I read PS4 messages from `/joy` and recorded my axis mapping.
* [ ] I controlled the turtle through my Python teleoperation node.
* [ ] I published turtle information on `/turtle_status`.
* [ ] I inspected the system with `rqt_graph` and `rqt_console`.
* [ ] I launched the complete system with one command.

**Next:** Complete the tasks in [exercise.md](exercise.md).
