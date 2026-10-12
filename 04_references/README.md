# References

**Navigation:** [Repository README](../README.md) · **References** · [ROS 2 Jazzy](ros2_jazzy.md) · [Gazebo Harmonic](gazebo_harmonic.md) · [URDF/Xacro/TF2](urdf_xacro_tf2.md) · [Nav2](nav2.md) · [MoveIt 2](moveit2.md)

---

Official documentation for the tools used in the course, organized by topic. Each weekly README links to the most relevant entries.

| File                                     | Topic                                              | Weeks   |
| ---------------------------------------- | -------------------------------------------------- | ------- |
| [ros2_jazzy.md](ros2_jazzy.md)           | ROS 2 Jazzy, installation, ros2_control, REPs      | All     |
| [gazebo_harmonic.md](gazebo_harmonic.md) | Gazebo Harmonic, `ros_gz`, Fortress → Harmonic porting notes | 2–4 |
| [urdf_xacro_tf2.md](urdf_xacro_tf2.md)   | URDF, Xacro, TF2, robot description tools          | 2–4     |
| [nav2.md](nav2.md)                       | Nav2, SLAM Toolbox, `robot_localization`           | 3       |
| [moveit2.md](moveit2.md)                 | MoveIt 2 tutorials and concepts                    | 4       |

## Version Rule

Always match the documentation to the course environment:

| Tool      | Use version            | Watch out for                                           |
| --------- | ---------------------- | ------------------------------------------------------- |
| ROS 2     | **Jazzy**              | Humble/Rolling pages with different commands            |
| Gazebo    | **Harmonic**           | Gazebo Classic (`gazebo_ros`) and Fortress (`ign`) tutorials |
| Nav2      | **`/jazzy/` pages**    | The site's default Rolling pages                        |
| MoveIt 2  | **`/main/` tutorials** (target Jazzy) | Humble-specific pages                        |

## How Links Are Verified

Links were checked in October 2026. The documentation sites could not be opened directly from the environment used to prepare this material, so each link was checked against the source that generates the page:

| Site                    | Checked against                                                                                   |
| ----------------------- | ------------------------------------------------------------------------------------------------- |
| `docs.ros.org/en/jazzy` | Source files in [ros2/ros2_documentation](https://github.com/ros2/ros2_documentation) (branch `jazzy`) |
| `gazebosim.org/docs/harmonic` | Source files in [gazebosim/docs](https://github.com/gazebosim/docs) (folder `harmonic`)     |
| `docs.nav2.org/jazzy`   | The published site files (branch `gh-pages` of [ros-navigation/docs.nav2.org](https://github.com/ros-navigation/docs.nav2.org)) |
| `control.ros.org/jazzy` | The published site files (branch `gh-pages` of [ros-controls/control.ros.org](https://github.com/ros-controls/control.ros.org)) |
| `moveit.picknik.ai/main` | Source files in [moveit/moveit2_tutorials](https://github.com/moveit/moveit2_tutorials) (branch `main`); URL pattern taken from links in that repository |
| GitHub repositories     | Repository, branch, and file paths checked with `git`                                             |
| REPs, ROS wiki          | URL forms as linked from the official ROS 2 Jazzy documentation; REP source files exist in [ros-infrastructure/rep](https://github.com/ros-infrastructure/rep) |

If a link stops working, please report it (see [CONTRIBUTING.md](../CONTRIBUTING.md)).
