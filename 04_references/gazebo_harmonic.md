# Gazebo Harmonic References

**Navigation:** [References](README.md) · [ROS 2 Jazzy](ros2_jazzy.md) · **Gazebo Harmonic** · [URDF/Xacro/TF2](urdf_xacro_tf2.md) · [Nav2](nav2.md) · [MoveIt 2](moveit2.md)

---

Gazebo Harmonic (`gz-sim` 8) is the Gazebo release paired with ROS 2 Jazzy. Many online tutorials are written for **Gazebo Classic** (Gazebo 11, `gazebo_ros`, `spawn_entity.py`) or **Fortress** (`ign gazebo`, `libignition-gazebo-*` plugins). Check the version before you follow one.

## Installation and Compatibility

| Topic                                                  | Link                                                    |
| ------------------------------------------------------ | ------------------------------------------------------- |
| Install Gazebo Harmonic                                | https://gazebosim.org/docs/harmonic/install             |
| ROS / Gazebo installation and compatible pairings      | https://gazebosim.org/docs/harmonic/ros_installation    |
| ROS 2 Gazebo vendor packages                           | https://gazebosim.org/docs/harmonic/ros2_gz_vendor_pkgs |
| Troubleshooting                                        | https://gazebosim.org/docs/harmonic/troubleshooting     |

## Simulation Tutorials

| Topic                                   | Link                                               | Weeks |
| --------------------------------------- | -------------------------------------------------- | ----- |
| Tutorials overview                      | https://gazebosim.org/docs/harmonic/tutorials      | 2     |
| Building your own robot (SDF)           | https://gazebosim.org/docs/harmonic/building_robot | 2     |
| Moving the robot (DiffDrive system)     | https://gazebosim.org/docs/harmonic/moving_robot   | 2     |
| SDF worlds                              | https://gazebosim.org/docs/harmonic/sdf_worlds     | 2, 3  |
| Sensors (LiDAR, IMU, contact)           | https://gazebosim.org/docs/harmonic/sensors        | 2, 3  |
| Spawn a URDF                            | https://gazebosim.org/docs/harmonic/spawn_urdf     | 2     |

## ROS 2 Integration (`ros_gz`)

| Topic                                              | Link                                                                  |
| -------------------------------------------------- | --------------------------------------------------------------------- |
| ROS 2 integration overview                         | https://gazebosim.org/docs/harmonic/ros2_overview                     |
| Launch Gazebo from ROS 2                           | https://gazebosim.org/docs/harmonic/ros2_launch_gazebo                |
| Use ROS 2 to interact with Gazebo (`ros_gz_bridge`) | https://gazebosim.org/docs/harmonic/ros2_integration                 |
| Use ROS 2 to spawn a Gazebo model                  | https://gazebosim.org/docs/harmonic/ros2_spawn_model                  |
| ROS 2 integration project template                 | https://gazebosim.org/docs/harmonic/ros_gz_project_template_guide     |

## Source Code

| Item                                         | Link                                                                                         |
| -------------------------------------------- | -------------------------------------------------------------------------------------------- |
| `ros_gz` (branch `jazzy`)                    | https://github.com/gazebosim/ros_gz/tree/jazzy                                               |
| `ros_gz_bridge` README (message type table)  | https://github.com/gazebosim/ros_gz/blob/jazzy/ros_gz_bridge/README.md                       |
| `gz_sim.launch.py` source (launch arguments) | https://github.com/gazebosim/ros_gz/blob/jazzy/ros_gz_sim/launch/gz_sim.launch.py.in         |
| DiffDrive system parameters (gz-sim 8)       | https://github.com/gazebosim/gz-sim/blob/gz-sim8/src/systems/diff_drive/DiffDrive.hh         |
| JointStatePublisher system parameters        | https://github.com/gazebosim/gz-sim/blob/gz-sim8/src/systems/joint_state_publisher/JointStatePublisher.hh |

## Porting Notes: Fortress → Harmonic

Observed in course testing (relevant to the [Trailobot](../01_course_documentation/course/robot_platforms/trailobot_amr.md) model):

| Fortress style                              | Harmonic style                        | Behavior in Harmonic                    |
| ------------------------------------------- | ------------------------------------- | --------------------------------------- |
| `libignition-gazebo-diff-drive-system.so`   | `gz-sim-diff-drive-system`            | Remapped, with a deprecation warning    |
| `ignition::gazebo::systems::DiffDrive`      | `gz::sim::systems::DiffDrive`         | Remapped, with a deprecation warning    |
| `<ignition_frame_id>`                       | `<gz_frame_id>`                       | `ignition_frame_id` is not applied; messages use the scoped Gazebo name |
| `IGN_GAZEBO_RESOURCE_PATH`                  | `GZ_SIM_RESOURCE_PATH`                | Use the `GZ_` name                      |

Verification notes: [README](README.md#how-links-are-verified).
