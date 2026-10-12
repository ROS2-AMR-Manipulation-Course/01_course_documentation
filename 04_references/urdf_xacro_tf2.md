# URDF, Xacro and TF2 References

**Navigation:** [References](README.md) · [ROS 2 Jazzy](ros2_jazzy.md) · [Gazebo Harmonic](gazebo_harmonic.md) · **URDF/Xacro/TF2** · [Nav2](nav2.md) · [MoveIt 2](moveit2.md)

---

These references support Week 2 (robot modeling) and are used again in Weeks 3 and 4.

## URDF and Xacro (ROS 2 Jazzy tutorials)

| Topic                                                 | Link                                                                                                                    |
| ----------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| URDF tutorials — overview                             | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/URDF-Main.html                                                 |
| Building a visual robot model from scratch            | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Building-a-Visual-Robot-Model-with-URDF-from-Scratch.html      |
| Building a movable robot model (joints, axes, limits) | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Building-a-Movable-Robot-Model-with-URDF.html                  |
| Adding physical and collision properties (inertia)    | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Adding-Physical-and-Collision-Properties-to-a-URDF-Model.html |
| Using Xacro to clean up a URDF                        | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Using-Xacro-to-Clean-Up-a-URDF-File.html                      |
| Using URDF with `robot_state_publisher` (Python)      | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Using-URDF-with-Robot-State-Publisher-py.html                 |
| Exporting a URDF file (from CAD)                      | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/URDF/Exporting-an-URDF-File.html                                   |

## Specifications

| Topic                             | Link                                   |
| --------------------------------- | -------------------------------------- |
| URDF XML specification (ROS wiki) | https://wiki.ros.org/urdf/XML          |
| URDF `<joint>` element            | https://wiki.ros.org/urdf/XML/joint    |
| REP 103 — units and conventions   | https://reps.openrobotics.org/rep-0103/ |
| REP 105 — mobile platform frames  | https://reps.openrobotics.org/rep-0105/ |

## TF2 (ROS 2 Jazzy)

| Topic                                 | Link                                                                                              |
| ------------------------------------- | ------------------------------------------------------------------------------------------------- |
| About TF2 (concepts)                  | https://docs.ros.org/en/jazzy/Concepts/Intermediate/About-Tf2.html                                 |
| TF2 tutorials — overview              | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Tf2-Main.html                             |
| Quaternion fundamentals               | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Quaternion-Fundamentals.html              |
| Debugging TF2 problems                | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Debugging-Tf2-Problems.html               |
| Writing a static broadcaster (Python) | https://docs.ros.org/en/jazzy/Tutorials/Intermediate/Tf2/Writing-A-Tf2-Static-Broadcaster-Py.html |

## Tool Repositories

| Package                     | Repository (branch used by Jazzy)                           |
| --------------------------- | ----------------------------------------------------------- |
| `xacro`                     | https://github.com/ros/xacro (`ros2`)                        |
| `robot_state_publisher`     | https://github.com/ros/robot_state_publisher (`jazzy`)       |
| `joint_state_publisher`     | https://github.com/ros/joint_state_publisher (`ros2`)        |
| `tf2_tools` (`view_frames`) | https://github.com/ros2/geometry2 (`jazzy`)                  |
| URDF tutorial examples      | https://github.com/ros/urdf_tutorial (`ros2`)                |

## Course Material

* [Week 2 lecture notes](../01_course_documentation/course/weeks/week02_kinematics_simulation/lecture/notes.md) — URDF, Xacro, TF2, inertia formulas
* [Week 2 kinematics examples](../01_course_documentation/course/weeks/week02_kinematics_simulation/lecture/kinematics_examples.md) — worked transforms and FK

Verification notes: [README](README.md#how-links-are-verified).
