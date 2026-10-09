# Week 2 Reference Links

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** [Week 2 README](../README.md) · [Lecture Notes](../lecture/notes.md) · [Lab Guide](../lab/lab_guide.md) · **References**

---

Use the **Jazzy** and **Harmonic** versions of every page. Many search results point to ROS 1, ROS 2 Humble, or **Gazebo Classic** (Gazebo 11, `gazebo_ros`, `spawn_entity.py`). Those instructions do not apply to this course.

> **How these links were checked (October 2026):** `docs.ros.org` and `gazebosim.org` could not be opened from the environment used to prepare this material. Each documentation link below was checked against the **source files** that generate the site, in [ros2/ros2_documentation](https://github.com/ros2/ros2_documentation) (branch `jazzy`) and [gazebosim/docs](https://github.com/gazebosim/docs) (folder `harmonic`). The URL pattern was confirmed from links inside the official documentation itself. GitHub repository, branch, and file links were checked with `git`. Please report any link that does not open.

---

## 1. ROS 2 Jazzy — Installation

| Topic                     | Link                                                                                   |
| ------------------------- | -------------------------------------------------------------------------------------- |
| Install ROS 2 Jazzy (Ubuntu deb packages) | https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html |
| ROS 2 Jazzy documentation home | https://docs.ros.org/en/jazzy/                                                    |

## 2. URDF and Xacro (ROS 2 Jazzy tutorials)

| Topic                                                   | Link                                                                                                                        |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| URDF tutorials — overview                               | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/URDF-Main.html                                                     |
| Building a visual robot model from scratch              | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Building-a-Visual-Robot-Model-with-URDF-from-Scratch.html          |
| Building a movable robot model (joints, axes, limits)   | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Building-a-Movable-Robot-Model-with-URDF.html                      |
| Adding physical and collision properties (inertia)      | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Adding-Physical-and-Collision-Properties-to-a-URDF-Model.html     |
| Using Xacro to clean up a URDF                          | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Using-Xacro-to-Clean-Up-a-URDF-File.html                          |
| Using URDF with `robot_state_publisher` (Python)        | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Using-URDF-with-Robot-State-Publisher-py.html                     |
| Exporting a URDF file (from CAD)                        | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Exporting-an-URDF-File.html                                       |

## 3. TF2 (ROS 2 Jazzy)

| Topic                              | Link                                                                                    |
| ---------------------------------- | --------------------------------------------------------------------------------------- |
| About TF2 (concepts)               | https://docs.ros.org/en/jazzy/Concepts/Intermediate/About-Tf2.html                       |
| TF2 tutorials — overview           | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Tf2-Main.html                   |
| Quaternion fundamentals            | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Quaternion-Fundamentals.html    |
| Debugging TF2 problems             | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Debugging-Tf2-Problems.html     |
| Writing a static broadcaster (Python) | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Writing-A-Tf2-Static-Broadcaster-Py.html |

## 4. Gazebo Harmonic

| Topic                                          | Link                                                       |
| ---------------------------------------------- | ---------------------------------------------------------- |
| Install Gazebo Harmonic                        | https://gazebosim.org/docs/harmonic/install                |
| ROS / Gazebo installation and compatible versions (Jazzy ↔ Harmonic) | https://gazebosim.org/docs/harmonic/ros_installation |
| ROS 2 Gazebo vendor packages                   | https://gazebosim.org/docs/harmonic/ros2_gz_vendor_pkgs     |
| Tutorials overview                             | https://gazebosim.org/docs/harmonic/tutorials              |
| Building your own robot (SDF)                  | https://gazebosim.org/docs/harmonic/building_robot         |
| Moving the robot (DiffDrive system)            | https://gazebosim.org/docs/harmonic/moving_robot           |
| SDF worlds                                     | https://gazebosim.org/docs/harmonic/sdf_worlds             |
| Sensors (LiDAR, IMU, contact)                  | https://gazebosim.org/docs/harmonic/sensors                |
| Spawn a URDF                                   | https://gazebosim.org/docs/harmonic/spawn_urdf             |
| Troubleshooting                                | https://gazebosim.org/docs/harmonic/troubleshooting        |

## 5. ROS 2 ↔ Gazebo Integration (`ros_gz`)

| Topic                                         | Link                                                                    |
| --------------------------------------------- | ----------------------------------------------------------------------- |
| ROS 2 integration overview                    | https://gazebosim.org/docs/harmonic/ros2_overview                       |
| Launch Gazebo from ROS 2 (`gz_sim.launch.py`) | https://gazebosim.org/docs/harmonic/ros2_launch_gazebo                  |
| Use ROS 2 to interact with Gazebo (`ros_gz_bridge`) | https://gazebosim.org/docs/harmonic/ros2_integration              |
| Use ROS 2 to spawn a Gazebo model (`create`)  | https://gazebosim.org/docs/harmonic/ros2_spawn_model                    |
| ROS 2 integration project template            | https://gazebosim.org/docs/harmonic/ros_gz_project_template_guide       |
| Setting up a robot simulation (ROS 2 docs)    | https://docs.ros.org/en/jazzy/Tutorials/Advanced/Simulators/Gazebo/Gazebo.html |
| `ros_gz` source (branch `jazzy`)              | https://github.com/gazebosim/ros_gz/tree/jazzy                          |
| `ros_gz_bridge` README (message type table)   | https://github.com/gazebosim/ros_gz/blob/jazzy/ros_gz_bridge/README.md  |
| `gz_sim.launch.py` source (launch arguments)  | https://github.com/gazebosim/ros_gz/blob/jazzy/ros_gz_sim/launch/gz_sim.launch.py.in |

## 6. Gazebo Systems Used in the Lab (source documentation)

The parameters of each system are documented in comments at the top of its header file (gz-sim 8 = Harmonic).

| System                  | Link                                                                                                      |
| ----------------------- | --------------------------------------------------------------------------------------------------------- |
| DiffDrive               | https://github.com/gazebosim/gz-sim/blob/gz-sim8/src/systems/diff_drive/DiffDrive.hh                       |
| JointStatePublisher     | https://github.com/gazebosim/gz-sim/blob/gz-sim8/src/systems/joint_state_publisher/JointStatePublisher.hh |

## 7. Standards and Specifications

| Topic                                                     | Link                                                |
| --------------------------------------------------------- | --------------------------------------------------- |
| REP 103 — Standard units of measure and coordinate conventions | https://reps.openrobotics.org/rep-0103/        |
| REP 105 — Coordinate frames for mobile platforms          | https://reps.openrobotics.org/rep-0105/              |
| URDF XML specification (ROS wiki)                         | https://wiki.ros.org/urdf/XML                        |
| URDF joint element                                        | https://wiki.ros.org/urdf/XML/joint                  |

The REP 105 link follows the same URL pattern as REP 103, which the ROS 2 Jazzy documentation links to; the REP 105 source file exists in [ros-infrastructure/rep](https://github.com/ros-infrastructure/rep). The URDF specification pages on the ROS wiki are also linked from the ROS 2 Jazzy documentation.

## 8. Tool and Package Repositories

| Package                 | Repository (branch used by Jazzy)                                  |
| ----------------------- | ------------------------------------------------------------------ |
| `xacro`                 | https://github.com/ros/xacro (branch `ros2`)                       |
| `robot_state_publisher` | https://github.com/ros/robot_state_publisher (branch `jazzy`)      |
| `joint_state_publisher` | https://github.com/ros/joint_state_publisher (branch `ros2`)       |
| `tf2_tools` (`view_frames`) | https://github.com/ros2/geometry2 (branch `jazzy`)             |
| `teleop_twist_keyboard` | https://github.com/ros2/teleop_twist_keyboard                      |
| URDF tutorial examples  | https://github.com/ros/urdf_tutorial (branch `ros2`)               |

## 9. Background Reading

| Topic                                   | Where                                                                             |
| --------------------------------------- | --------------------------------------------------------------------------------- |
| Moments of inertia of common shapes     | Any first-year mechanics textbook; the formulas used are in [Lecture Notes §8.4](../lecture/notes.md#84-inertial-properties) |
| Homogeneous transforms and forward kinematics | Introductory robotics textbooks (chapter on rigid-body motion / forward kinematics); see also [Kinematics Examples](../lecture/kinematics_examples.md) |
