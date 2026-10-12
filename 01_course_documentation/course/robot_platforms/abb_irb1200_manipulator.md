# ABB IRB 1200 — Industrial Manipulator

**Navigation:** [Robot Platforms](README.md) · [Trailobot](trailobot_amr.md) · **ABB IRB 1200** · [RB3-1200](rb3_1200_manipulator.md) · [Simulation guide](../../../02_robot_simulation/abb_irb1200/README.md)

<p align="center">
  <img src="../../../assets/images/robots/abb_irb1200.png" alt="ABB IRB 1200 industrial manipulator" height="280">
  <br><sub>ABB IRB 1200 — 6-axis industrial manipulator</sub>
</p>

> **Robot description source:** IFRA-Cranfield, *ROS 2 Sim-to-Real Robot Control* — [github.com/IFRA-Cranfield/ros2_SimRealRobotControl](https://github.com/IFRA-Cranfield/ros2_SimRealRobotControl), branch **`jazzy`**
> **Inspected version:** commit `b8fa70d` (September 2026) · **License:** Apache-2.0
> The robot description is in the package **`ros2srrc_robots`** (folder `ros2srrc_robots/irb1200/`) inside that repository. There is no separate public repository named `ros2srrc_robots`.

---

## 1. Educational Purpose

The IRB 1200 is a 6-axis industrial arm. In this course it is used to:

* **Week 2:** show how URDF joint origins, axes, and limits describe a real serial manipulator (compared with the 2-link arm in the [kinematics examples](../weeks/week02_kinematics_simulation/lecture/kinematics_examples.md)).
* **Week 4:** study 6-DoF forward and inverse kinematics, tool frames, singularities, and motion planning with MoveIt 2 in simulation.

Hardware operation of a physical IRB 1200 is outside the scope of the course labs.

---

## 2. Source Repository Facts (✅ confirmed from the files)

| Item                    | Value                                                                                         |
| ----------------------- | --------------------------------------------------------------------------------------------- |
| Target environment      | Ubuntu 24.04, ROS 2 Jazzy, Gazebo Harmonic, MoveIt 2 (repository README and `instructions/Installation.md`, branch `jazzy`) |
| Robot description       | `ros2srrc_robots/irb1200/urdf/irb1200_macro.urdf.xacro` (+ `irb1200_ros2control.xacro`, `irb1200_transmission.xacro`), meshes in `ros2srrc_robots/irb1200/meshes/` |
| Robot package           | `packages/ros2srrc_irb1200` with `urdf/irb1200.urdf.xacro` and `urdf/irb1200_egp64.urdf.xacro` |
| Configurations          | `irb1200_1`: IRB 1200 on a robot stand · `irb1200_2`: IRB 1200 + Schunk EGP-64 gripper on a robot stand (`config/configurations.yaml`) |
| MoveIt 2 configuration  | `ros2srrc_moveit/config/irb1200.srdf`, `irb1200_egp64.srdf`                                  |
| Launch (generic)        | `ros2srrc_launch` package: `simulation.launch.py`, `moveit2.launch.py`, `bringup.launch.py` with `package:=` and `config:=` arguments |

Example documented in the repository's [`instructions/ROS2EnvironmentLaunch.md`](https://github.com/IFRA-Cranfield/ros2_SimRealRobotControl/blob/jazzy/instructions/ROS2EnvironmentLaunch.md):

```bash
# ABB IRB-1200 Robot w/ Schunk EGP-64 Parallel Gripper (moveit2)
ros2 launch ros2srrc_launch moveit2.launch.py package:=ros2srrc_irb1200 config:=irb1200_2
```

The repository's launch files have **not** been run by the course maintainers. Follow the repository's own [installation instructions](https://github.com/IFRA-Cranfield/ros2_SimRealRobotControl/blob/jazzy/instructions/Installation.md).

---

## 3. Kinematic Structure (✅ from `irb1200_macro.urdf.xacro`)

| Joint     | Type     | Origin `xyz` (m) relative to previous link | Axis    | Limits (rad)        |
| --------- | -------- | ------------------------------------------ | ------- | ------------------- |
| `joint_1` | revolute | (0, 0, 0.3991)                              | z       | −2.967 … 2.967      |
| `joint_2` | revolute | (0, 0, 0)                                   | y       | −1.745 … 2.269      |
| `joint_3` | revolute | (0, 0, 0.448)                               | y       | −3.491 … 1.222      |
| `joint_4` | revolute | (0, 0, 0.042)                               | x       | −4.712 … 4.712      |
| `joint_5` | revolute | (0.451, 0, 0)                               | y       | −2.269 … 2.269      |
| `joint_6` | revolute | (0.082, 0, 0)                               | x       | −6.981 … 6.981      |

Frame chain: `base_link → link_1 … link_6 → flange → tool0`. `tool0` is rotated by 90° about y relative to `link_6` (`rpy="0 π/2 0"`), so the tool's z axis points along the arm.

These are the **model's** values. For safety-critical work, always use the manufacturer's official data.

## 4. Course Verification (🧪 tested)

Tested in October 2026 with ROS 2 Jazzy (RoboStack distribution); the repository was not built with `colcon`.

| Check                                                                 | Result |
| --------------------------------------------------------------------- | ------ |
| `xacro packages/ros2srrc_irb1200/urdf/irb1200.urdf.xacro`             | ✅ Generates URDF |
| `check_urdf`                                                          | ✅ Valid: `world → robot_stand → base_link → link_1 … link_6 → flange → tool0` |
| Forward kinematics at zero joint angles (`robot_state_publisher` + `tf2_echo base_link tool0`) | ✅ (0.533, 0.000, 0.889) m, pitch 90° — equals the sum of the joint origins: x = 0.451 + 0.082, z = 0.3991 + 0.448 + 0.042 |
| Gazebo Harmonic simulation and MoveIt 2 launch                        | Not tested |

The zero-pose check is a good first Week 4 exercise: students can predict the tool position from the table above before running TF2.

## 5. Related Documents

* [IRB 1200 simulation guide](../../../02_robot_simulation/abb_irb1200/README.md)
* [Week 4 — Manipulator Kinematics & Motion Planning](../weeks/week04_manipulator_motion_planning/README.md)
* [MoveIt 2 references](../../../04_references/moveit2.md)
