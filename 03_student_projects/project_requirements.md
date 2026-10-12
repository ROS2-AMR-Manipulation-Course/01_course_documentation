# Final Project Requirements

**Navigation:** [Student Projects](README.md) · **Requirements** · [Submission Guide](submission_guide.md) · [Rubric](project_rubric.md)

---

> Team size, deadlines, and the list of approved project topics are announced by the instructor. The requirements below apply to every project.

## 1. Goal

Build a small but complete ROS 2 robotic system that combines what you learned in the four weeks:

```text
ROS 2 (Week 1) → Robot model + simulation (Week 2) → Navigation (Week 3) and/or Manipulation (Week 4) → Integrated system
```

The project is judged on **understanding and engineering quality**, not on size. A smaller system that works reliably, is measured, and is well explained scores higher than a large system nobody can reproduce.

## 2. Project Tracks

Choose one track (or propose your own for approval):

| Track                     | Minimum scope                                                                                   | Platform suggestions |
| ------------------------- | ----------------------------------------------------------------------------------------------- | -------------------- |
| A — Mobile robot          | Simulated AMR that maps an environment and navigates autonomously to several goals              | Trailobot (when the Harmonic port is available) or your own differential-drive model |
| B — Manipulator           | Simulated 6-DoF arm that plans collision-free motions to several target poses with MoveIt 2     | ABB IRB 1200         |
| C — Integrated            | Combination of A and B (for example, a mobile robot delivers to a manipulator workspace), or an extension of the Week 2 robot with new sensors and behaviors | Any course platform |

## 3. Mandatory Requirements

Every project must:

1. **Run on the course environment**: Ubuntu 24.04, ROS 2 Jazzy, Gazebo Harmonic ([software requirements](../01_course_documentation/course/software_requirements.md)).
2. **Be organized as ROS 2 packages** that build with `colcon build`.
3. **Start with launch files**: one command for the main demonstration.
4. **Include a valid robot description** (URDF/Xacro) that passes `check_urdf`, or reference an external description with its version.
5. **Have a correct TF tree**, documented with a `view_frames` output.
6. **Use at least one sensor** in simulation and process its data.
7. **Use parameters or configuration files** instead of hard-coded values for tunable settings.
8. **Include a measured evaluation**: define success criteria before testing, and report results (for example, goal success rate over N trials, position error, planning time).
9. **Be reproducible** from the README alone ([submission guide](submission_guide.md)).
10. **Credit external code**: list every external package or repository used, with its version or commit and license.

## 4. Optional Extensions

Extensions can raise the score in the relevant rubric criteria but do not replace mandatory requirements. Examples:

* A custom Nav2 configuration (costmap layers, controller tuning) with a comparison of results.
* A pick-and-place sequence with MoveIt 2.
* A custom ROS 2 interface (message, service, or action).
* Sensor fusion (for example, wheel odometry + IMU with `robot_localization`).
* Automated tests (`colcon test`) or a CI workflow.

## 5. Deliverables

| Deliverable                   | Description                                                      |
| ----------------------------- | ---------------------------------------------------------------- |
| Git repository                | Source, configuration, launch files, README ([submission guide](submission_guide.md)) |
| Report                        | Design, implementation, evaluation, limitations (4–8 pages or equivalent Markdown) |
| Demonstration video           | 2–5 minutes, showing the main launch command and the result     |
| Presentation                  | Live or recorded, followed by technical questions               |

Grading: [project_rubric.md](project_rubric.md).
