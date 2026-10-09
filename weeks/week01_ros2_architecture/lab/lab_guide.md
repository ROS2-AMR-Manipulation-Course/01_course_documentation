# Week 1 Lab Guide

## ROS 2 Communication, Python Nodes, PS4 Teleoperation and Launch Files

**Platform:** Ubuntu 24.04, ROS 2 Jazzy, Turtlesim
**Estimated duration:** 8 hours
**Main objective:** Build a small ROS 2 system step by step, starting with simple communication and finishing with PS4-controlled Turtlesim.

---

## 1. Laboratory Overview

In this lab, you will learn how ROS 2 nodes communicate using topics, services, actions, parameters, and launch files.

You will first test existing ROS 2 tools, then create small Python programs. Finally, you will integrate the nodes into one system that uses a PS4 controller to move a turtle and publishes its status.

### Final system architecture

```text
PS4 Controller
      |
      v
   joy_node
      |
      | /joy
      v
turtle_ps4_teleop
      |
      | /turtle1/cmd_vel
      v
  turtlesim_node
      |
      | /turtle1/pose
      v
turtle_status_node
      |
      | /turtle_status
      v
turtle_status_monitor

Additional communication:
- Service: /reset or a custom reset service
- Action: /turtle1/rotate_absolute
- Visualization: rqt_graph
- Logging: rqt_console
- Startup: ros2 launch
```

## 2. Check the ROS 2 Environment

Open a terminal and source ROS 2 Jazzy.

```bash
source /opt/ros/jazzy/setup.bash
echo $ROS_DISTRO
ros2 --help
```

Expected distribution:

```text
jazzy
```

Create a workspace:

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
source install/setup.bash
```

If `colcon` is missing, install it:

```bash
sudo apt update
sudo apt install python3-colcon-common-extensions
```

**Checkpoint:** You can source ROS 2 Jazzy and build an empty workspace.

## 3. Start Turtlesim and Inspect the ROS Graph

Open Terminal 1:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run turtlesim turtlesim_node
```

Open Terminal 2:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run turtlesim turtle_teleop_key
```

Use the keyboard teleoperation terminal to move the turtle.

In Terminal 3, inspect the graph:

```bash
ros2 node list
ros2 topic list
ros2 service list
ros2 action list
ros2 param list /turtlesim
```

Inspect the important topics:

```bash
ros2 topic type /turtle1/pose
ros2 topic type /turtle1/cmd_vel
ros2 interface show turtlesim/msg/Pose
ros2 interface show geometry_msgs/msg/Twist
```

Monitor the turtle's position:

```bash
ros2 topic echo /turtle1/pose
```

Stop the command with `Ctrl+C`.

**Checkpoint:** Explain which node publishes the pose and which topic receives velocity commands.

## 4. Test a Publisher and Subscriber Using CLI Tools

Before writing code, test ROS 2 communication from the terminal.

### 4.1 Start a subscriber

```bash
ros2 topic echo /lab_message
```

### 4.2 Publish one message

Open another terminal:

```bash
source /opt/ros/jazzy/setup.bash
ros2 topic pub --once /lab_message std_msgs/msg/String "{data: 'Hello ROS 2'}"
```

The subscriber should display the message.

### 4.3 Inspect the topic

```bash
ros2 topic info /lab_message
ros2 topic type /lab_message
ros2 interface show std_msgs/msg/String
```

**Checkpoint:** Explain the publisher, subscriber, topic name, and message type.

## 5. Create a Simple Python Publisher and Subscriber

Create a package:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python ros2_lab_basics --dependencies rclpy std_msgs
```

### 5.1 Create the publisher

Create `ros2_lab_basics/simple_publisher.py`:

```python
import rclpy
from rclpy.node import Node
from std_msgs.msg import String


class SimplePublisher(Node):
    def __init__(self):
        super().__init__('simple_publisher')
        self.publisher = self.create_publisher(
            String, '/lab_message', 10
        )
        self.timer = self.create_timer(1.0, self.publish_message)
        self.counter = 0

    def publish_message(self):
        msg = String()
        msg.data = f'Hello ROS 2: {self.counter}'
        self.publisher.publish(msg)
        self.get_logger().info(f'Published: {msg.data}')
        self.counter += 1


def main(args=None):
    rclpy.init(args=args)
    node = SimplePublisher()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

### 5.2 Create the subscriber

Create `ros2_lab_basics/simple_subscriber.py`:

```python
import rclpy
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
    except KeyboardInterrupt:
        pass
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

### 5.3 Register the commands

Edit `setup.py`. Add these entries to the existing `entry_points` configuration:

```python
entry_points={
    'console_scripts': [
        'simple_publisher = ros2_lab_basics.simple_publisher:main',
        'simple_subscriber = ros2_lab_basics.simple_subscriber:main',
    ],
},
```

Keep the other existing `setup.py` configuration, including the package's `data_files`.

### 5.4 Build and test

```bash
cd ~/ros2_ws
colcon build --packages-select ros2_lab_basics
source install/setup.bash
```

Terminal 1:

```bash
ros2 run ros2_lab_basics simple_subscriber
```

Terminal 2:

```bash
ros2 run ros2_lab_basics simple_publisher
```

**Checkpoint:** Both nodes run and messages arrive every second.

## 6. Test Services

ROS 2 services use request/response communication.

### 6.1 Inspect Turtlesim services

Start Turtlesim if it is not already running.

```bash
ros2 service list
ros2 service type /reset
ros2 service type /clear
ros2 service type /turtle1/set_pen
```

Inspect service definitions:

```bash
ros2 interface show std_srvs/srv/Empty
ros2 interface show turtlesim/srv/SetPen
```

### 6.2 Call a built-in service

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
ros2 service call /turtle1/set_pen turtlesim/srv/SetPen "{r: 255, g: 0, b: 0, width: 3, off: 0}"
```

**Checkpoint:** Explain why a service is suitable for requesting a reset.

## 7. Create a Python Service Server and Client

Create another package:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python ros2_lab_services --dependencies rclpy example_interfaces
```

### 7.1 Create the service server

Create `ros2_lab_services/add_two_ints_server.py`:

```python
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts


class AddTwoIntsServer(Node):
    def __init__(self):
        super().__init__('add_two_ints_server')
        self.server = self.create_service(
            AddTwoInts, '/add_two_ints', self.add_callback
        )

    def add_callback(self, request, response):
        response.sum = request.a + request.b
        self.get_logger().info(
            f'{request.a} + {request.b} = {response.sum}'
        )
        return response


def main(args=None):
    rclpy.init(args=args)
    node = AddTwoIntsServer()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

### 7.2 Create the service client

Create `ros2_lab_services/add_two_ints_client.py`:

```python
import rclpy
from rclpy.node import Node
from example_interfaces.srv import AddTwoInts


class AddTwoIntsClient(Node):
    def __init__(self):
        super().__init__('add_two_ints_client')
        self.client = self.create_client(AddTwoInts, '/add_two_ints')

    def send_request(self, a, b):
        while not self.client.wait_for_service(timeout_sec=1.0):
            self.get_logger().info('Waiting for service...')

        request = AddTwoInts.Request()
        request.a = a
        request.b = b
        future = self.client.call_async(request)
        rclpy.spin_until_future_complete(self, future)

        if future.result() is not None:
            self.get_logger().info(f'Result: {future.result().sum}')
        else:
            self.get_logger().error('Service call failed')


def main(args=None):
    rclpy.init(args=args)
    node = AddTwoIntsClient()
    try:
        node.send_request(10, 20)
    finally:
        node.destroy_node()
        rclpy.shutdown()


if __name__ == '__main__':
    main()
```

Register both executables in `setup.py`:

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
colcon build --packages-select ros2_lab_services
source install/setup.bash
```

Run the server in Terminal 1:

```bash
ros2 run ros2_lab_services add_two_ints_server
```

Run the client in Terminal 2:

```bash
ros2 run ros2_lab_services add_two_ints_client
```

Expected result:

```text
Result: 30
```

**Checkpoint:** The client sends two numbers and receives their sum from the server.

## 8. Inspect and Test an Action

Actions are suitable for longer tasks that may provide feedback or be cancelled.

Turtlesim provides `/turtle1/rotate_absolute`.

Inspect it:

```bash
ros2 action list
ros2 action info /turtle1/rotate_absolute
ros2 action send_goal --help
```

Inspect the action type:

```bash
ros2 action list -t
```

Use the action type shown by the command in the next step. For the standard Turtlesim action, it is `turtlesim/action/RotateAbsolute`.

```bash
ros2 interface show turtlesim/action/RotateAbsolute
```

Send a goal:

```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}" --feedback
```

Try another angle, then observe the turtle's orientation.

**Checkpoint:** Identify the goal, feedback, and result in the action interface.

## 9. Read PS4 Controller Input with `joy_node`

Install the relevant packages:

```bash
sudo apt update
sudo apt install ros-jazzy-joy
```

Connect your PS4 controller through USB or Bluetooth. Confirm that Ubuntu can detect it before continuing.

Start the joystick node:

```bash
source /opt/ros/jazzy/setup.bash
ros2 run joy joy_node
```

In another terminal:

```bash
ros2 topic list
ros2 topic info /joy
ros2 topic echo /joy
```

Move each stick and press each button. Observe changes to the `axes` and `buttons` arrays.

**Do not assume the axis indexes.** Record the indexes for forward/backward movement and turning from your own controller output. Controller mappings can vary.

If `/joy` is missing, check the controller connection and `joy_node` terminal output first.

**Checkpoint:** You can identify the relevant axes and a button from actual `/joy` messages.

## 10. Create the PS4-to-Turtlesim Teleoperation Node

Create a package:

```bash
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python turtle_ps4_lab --dependencies rclpy sensor_msgs geometry_msgs std_msgs turtlesim
```

Create `turtle_ps4_lab/turtle_ps4_teleop.py`:

```python
import rclpy
from rclpy.node import Node
from sensor_msgs.msg import Joy
from geometry_msgs.msg import Twist


class TurtlePS4Teleop(Node):
    def __init__(self):
        super().__init__('turtle_ps4_teleop')

        # Update these indexes after inspecting your own /joy output.
        self.declare_parameter('linear_axis', 1)
        self.declare_parameter('angular_axis', 0)
        self.declare_parameter('linear_scale', 2.0)
        self.declare_parameter('angular_scale', 2.0)

        self.linear_axis = self.get_parameter('linear_axis').value
        self.angular_axis = self.get_parameter('angular_axis').value
        self.linear_scale = self.get_parameter('linear_scale').value
        self.angular_scale = self.get_parameter('angular_scale').value

        self.publisher = self.create_publisher(
            Twist, '/turtle1/cmd_vel', 10
        )
        self.subscription = self.create_subscription(
            Joy, '/joy', self.joy_callback, 10
        )

        self.get_logger().info('PS4 teleoperation node started')

    def joy_callback(self, msg):
        if max(self.linear_axis, self.angular_axis) >= len(msg.axes):
            self.get_logger().error('Configured axis index is out of range')
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
    except KeyboardInterrupt:
        pass
    finally:
        # Stop the turtle when the teleoperation node shuts down.
        node.publisher.publish(Twist())
        node.destroy_node()
        rclpy.shutdown()


if __name__ == '__main__':
    main()
```

Register this executable in `setup.py`:

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
colcon build --packages-select turtle_ps4_lab
source install/setup.bash
```

Run the nodes in separate terminals:

```bash
ros2 run turtlesim turtlesim_node
ros2 run joy joy_node
ros2 run turtle_ps4_lab turtle_ps4_teleop
```

Move the stick and observe the turtle. If the directions are wrong, adjust `linear_axis`, `angular_axis`, or their scales.

For example, if your forward/backward axis is index `1` and your turning axis is index `0`, the defaults may work. If not, use the indexes you recorded in Section 9.

**Safety note:** This controls a simulated turtle, but test one axis at a time and keep the controller centered before changing mappings. A dead-man button and explicit zero-velocity handling are good improvements for a later exercise.

## 11. Publish Turtle Status

The Turtlesim node publishes `/turtle1/pose`. Create a status node that subscribes to the pose and publishes a human-readable status string.

Create `turtle_ps4_lab/turtle_status_node.py`:

```python
import rclpy
from rclpy.node import Node
from turtlesim.msg import Pose
from std_msgs.msg import String


class TurtleStatusNode(Node):
    def __init__(self):
        super().__init__('turtle_status_node')

        self.publisher = self.create_publisher(
            String, '/turtle_status', 10
        )
        self.subscription = self.create_subscription(
            Pose, '/turtle1/pose', self.pose_callback, 10
        )

    def pose_callback(self, pose):
        msg = String()
        msg.data = (
            f'x={pose.x:.2f}, y={pose.y:.2f}, '
            f'theta={pose.theta:.2f}, '
            f'linear_velocity={pose.linear_velocity:.2f}, '
            f'angular_velocity={pose.angular_velocity:.2f}'
        )
        self.publisher.publish(msg)
        self.get_logger().info(msg.data)


def main(args=None):
    rclpy.init(args=args)
    node = TurtleStatusNode()
    try:
        rclpy.spin(node)
    except KeyboardInterrupt:
        pass
    node.destroy_node()
    rclpy.shutdown()


if __name__ == '__main__':
    main()
```

Add another console entry:

```python
'turtle_status_node = turtle_ps4_lab.turtle_status_node:main',
```

Rebuild:

```bash
cd ~/ros2_ws
colcon build --packages-select turtle_ps4_lab
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
```

**Checkpoint:** The status updates as the turtle moves.

## 12. Add Logging and Inspect the Graph

Run:

```bash
ros2 run rqt_graph rqt_graph
```

If necessary, install the tool:

```bash
sudo apt install ros-jazzy-rqt-graph
```

Open the logging tool:

```bash
ros2 run rqt_console rqt_console
```

If needed, install it:

```bash
sudo apt install ros-jazzy-rqt-console
```

Observe messages from the Python nodes. The teleoperation and status nodes use `get_logger().info(...)`, so they should generate messages for the ROS logging system.

Useful inspection commands:

```bash
ros2 node list
ros2 topic list
ros2 topic info /joy
ros2 topic info /turtle1/cmd_vel
ros2 topic info /turtle1/pose
ros2 topic info /turtle_status
```

## 13. Create a Launch File

The goal is to start the Turtlesim application, joystick node, teleoperation node, and status node from one command.

Inside `turtle_ps4_lab`, create:

```text
turtle_ps4_lab/
├── __init__.py
├── turtle_ps4_teleop.py
└── turtle_status_node.py

launch/
└── turtle_system.launch.py
```

Create `launch/turtle_system.launch.py`:

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

**Important:** The axis indexes in this launch file are examples. Replace them with the indexes you discovered for your controller.

### Install the launch file

In `setup.py`, make sure the launch file is installed. Add `import os` and `from glob import glob` if they are not already present, then include this entry in `data_files`:

```python
(
    os.path.join('share', package_name, 'launch'),
    glob('launch/*.launch.py'),
),
```

Keep the package's existing `data_files` entries, including its resource marker and `package.xml`.

Make sure `package.xml` includes runtime dependencies for `launch`, `launch_ros`, `joy`, and `turtlesim` as appropriate for your package. The Python package creation command already adds the dependencies you supplied; add any missing launch dependencies.

Rebuild and source:

```bash
cd ~/ros2_ws
colcon build --packages-select turtle_ps4_lab
source install/setup.bash
```

Start the complete system:

```bash
ros2 launch turtle_ps4_lab turtle_system.launch.py
```

Verify:

```bash
ros2 node list
ros2 topic list
ros2 topic echo /turtle_status
```

Use `rqt_graph` to inspect the integrated system.

**Checkpoint:** One launch command starts all four nodes, and the PS4 controller moves the turtle while `/turtle_status` updates.

## 14. Troubleshooting

| Problem                             | What to check                                         |
| ----------------------------------- | ----------------------------------------------------- |
| `ROS_DISTRO` is empty               | Source `/opt/ros/jazzy/setup.bash`                    |
| Package command not found           | Rebuild the workspace and source `install/setup.bash` |
| `/joy` does not appear              | Check the controller connection and `joy_node` output |
| Turtle does not move                | Inspect `/joy`, then inspect `/turtle1/cmd_vel`       |
| Turtle moves in the wrong direction | Verify axis indexes and signs                         |
| Status topic is missing             | Check the status node and `/turtle1/pose`             |
| Launch file cannot be found         | Check `setup.py` launch installation and rebuild      |
| `rqt_console` is empty              | Confirm the nodes are running and publishing logs     |

## 15. Final Checklist

* [ ] I can inspect nodes, topics, services, actions, and parameters.
* [ ] I tested a CLI publisher and subscriber.
* [ ] I wrote a Python publisher and subscriber.
* [ ] I called a built-in Turtlesim service.
* [ ] I created and tested a Python service server and client.
* [ ] I inspected and called the Turtlesim rotate action.
* [ ] I read PS4 messages from `/joy`.
* [ ] I controlled the turtle through my Python teleoperation node.
* [ ] I published turtle information on `/turtle_status`.
* [ ] I inspected the system with `rqt_graph` and `rqt_console`.
* [ ] I launched the complete system with one command.
