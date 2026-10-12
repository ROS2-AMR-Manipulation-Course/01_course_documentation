# Robot Platforms

**Navigation:** [Course README](../README.md) · **Robot Platforms** · [Trailobot](trailobot_amr.md) · [ABB IRB 1200](abb_irb1200_manipulator.md) · [RB3-1200](rb3_1200_manipulator.md)

<table>
  <tr>
    <td align="center"><img src="../../../assets/images/robots/trailobot_isometric.jpg" alt="Trailobot AMR, three-quarter view" height="180"></td>
    <td align="center"><img src="../../../assets/images/robots/abb_irb1200.png" alt="ABB IRB 1200 industrial manipulator" height="180"></td>
    <td align="center"><img src="../../../assets/images/robots/rb3_1200.png" alt="Rainbow Robotics RB3-1200 collaborative manipulator" height="180"></td>
  </tr>
</table>

<p align="center"><sub>Trailobot AMR · ABB IRB 1200 · RB3-1200</sub></p>

---

The course uses one mobile robot and two manipulators. The implementations live in their **original repositories**; this folder explains what each robot is, how the course uses it, and what has been verified.

| Platform | Type | Weeks | Source | Documentation status |
| -------- | ---- | ----- | ------ | -------------------- |
| [Trailobot](trailobot_amr.md) | Differential-drive AMR, 2 LiDARs, 2 RGB-D cameras, IMU, 8 ultrasonic sensors | 1, 2, 3 | [boyloy21/Trailobot_Simulation](https://github.com/boyloy21/Trailobot_Simulation) | Inspected; model validated on Jazzy; Harmonic port needed for Week 3 |
| [ABB IRB 1200](abb_irb1200_manipulator.md) | 6-DoF industrial manipulator | 2, 4 | [IFRA-Cranfield/ros2_SimRealRobotControl](https://github.com/IFRA-Cranfield/ros2_SimRealRobotControl) (branch `jazzy`, package `ros2srrc_robots`) | Inspected; model validated on Jazzy; FK checked |
| [RB3-1200](rb3_1200_manipulator.md) | 6-DoF collaborative manipulator (Rainbow Robotics RB series) | 4 | Not yet available | Details pending from the course maintainer |

## How Platforms Map to the Weeks

```text
Week 1  ROS 2 architecture ────────── Trailobot as an example node system
Week 2  Modeling & simulation ─────── teaching robot diffbot (built in the lab)
                                      + Trailobot description, IRB 1200 joint chain
Week 3  SLAM & navigation ─────────── Trailobot in simulation (planned)
Week 4  Manipulation ──────────────── ABB IRB 1200, RB3-1200 with MoveIt 2 (planned)
```

Week 2 deliberately uses a small robot that students build themselves (`diffbot`, same structure as Trailobot), so they understand every line of the model before working with a full robot description.

## Labels Used in Platform Pages

| Label | Meaning |
| ----- | ------- |
| ✅ Confirmed | Present in the source repository files |
| 🧪 Tested | Checked by the course maintainers, with the test conditions stated |
| 📝 Proposed | Planned course activity, not yet implemented |
| ⚠️ Issue | Mismatch or problem found |

Running instructions: [02_robot_simulation](../../../02_robot_simulation/README.md).
