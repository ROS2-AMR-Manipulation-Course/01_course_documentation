# ROS 2 AMR & Manipulation Course

A four-week, hands-on course on **ROS 2**, **Autonomous Mobile Robots (AMRs)**, **robot kinematics and simulation**, and **manipulator motion planning**.

| | |
|---|---|
| **Format** | 4 weeks · 4 h lecture + 8 h laboratory per week (48 contact hours) + final project |
| **Environment** | Ubuntu 24.04 LTS · ROS 2 Jazzy · Gazebo Harmonic · RViz2 · URDF/Xacro · TF2 · Nav2 · MoveIt 2 |
| **Robots** | Trailobot AMR · ABB IRB 1200 · RB3-1200 |
| **Audience** | Engineering students with basic Linux, Python, and mathematics; no prior ROS experience needed |

---

## Four-Week Roadmap

| Week | Topic | What students build | Platform | Status |
| ---- | ----- | ------------------- | -------- | ------ |
| 1 | [Linux, Middleware & ROS 2 Architecture](01_course_documentation/course/weeks/week01_ros2_architecture/README.md) | A multi-node ROS 2 system: Python nodes, services, actions, parameters, PS4-controlled Turtlesim, launch files | ROS 2 / Turtlesim | ✅ Lecture + lab |
| 2 | [Kinematic Modeling & Physics Simulation](01_course_documentation/course/weeks/week02_kinematics_simulation/README.md) | A differential-drive robot modeled in URDF/Xacro, validated, inspected with TF2, and driven in Gazebo Harmonic | `diffbot` (built in the lab), Trailobot model | ✅ Lecture + lab |
| 3 | [SLAM & Autonomous Navigation](01_course_documentation/course/weeks/week03_slam_navigation/README.md) | Mapping, localization, and Nav2 navigation in simulation | Trailobot | 📝 Lecture outline · lab planned |
| 4 | [Manipulator Kinematics & Motion Planning](01_course_documentation/course/weeks/week04_manipulator_motion_planning/README.md) | FK/IK and collision-free motion planning with MoveIt 2 | ABB IRB 1200, RB3-1200 | 📝 Lecture outline · lab planned |

Then: an [integrated final project](03_student_projects/README.md).

---

## Repository Structure

```text
.
├── README.md                      This file
├── LICENSE                        CC BY 4.0
├── CONTRIBUTING.md                How to report problems and contribute
├── 01_course_documentation/
│   └── course/                    Syllabus, outcomes, software, assessment, robot platforms
│       ├── robot_platforms/       Trailobot, ABB IRB 1200, RB3-1200
│       └── weeks/                 week01 … week04: README, lecture/, lab/
├── 02_robot_simulation/           How to obtain and run each robot's simulation
├── 03_student_projects/           Final project requirements, submission guide, rubric
└── 04_references/                 Verified links to official documentation
```

| Start here | |
|---|---|
| Course overview and navigation | [01_course_documentation/course/README.md](01_course_documentation/course/README.md) |
| Syllabus and prerequisites | [course_syllabus.md](01_course_documentation/course/course_syllabus.md) |
| Install the environment | [software_requirements.md](01_course_documentation/course/software_requirements.md) |
| How work is graded | [assessment_policy.md](01_course_documentation/course/assessment_policy.md) |
| Robot platforms | [robot_platforms/README.md](01_course_documentation/course/robot_platforms/README.md) |

---

## Robot Platforms

| Robot | Type | Implementation (external repository) |
| ----- | ---- | ------------------------------------ |
| [Trailobot](01_course_documentation/course/robot_platforms/trailobot_amr.md) | Differential-drive AMR with dual LiDAR, RGB-D cameras, IMU, ultrasonic sensors | [boyloy21/Trailobot_Simulation](https://github.com/boyloy21/Trailobot_Simulation) |
| [ABB IRB 1200](01_course_documentation/course/robot_platforms/abb_irb1200_manipulator.md) | 6-DoF industrial manipulator | [IFRA-Cranfield/ros2_SimRealRobotControl](https://github.com/IFRA-Cranfield/ros2_SimRealRobotControl) (branch `jazzy`) |
| [RB3-1200](01_course_documentation/course/robot_platforms/rb3_1200_manipulator.md) | 6-DoF collaborative manipulator (Rainbow Robotics RB series) | Robot description not yet available |

Robot code stays in its original repository. This repository contains the course documentation and references those implementations.

---

## Lecture Slides

Each week's `lecture/` folder has a `README.md` describing the lecture. **Slides (PDF) are uploaded by the course maintainer** when they are ready. Weeks 1 and 2 already have slides.

## Testing Status

Commands and files are marked as tested only where they were actually run. The Week 2 lab package was built and run with ROS 2 Jazzy and Gazebo Harmonic (headless, software rendering). It still needs one run-through on the lab computers with the apt packages. Robot-platform pages state exactly what was verified.

## License and Contributing

The course documentation is licensed under [CC BY 4.0](LICENSE). External robot repositories keep their own licenses. To report a problem or suggest an improvement, see [CONTRIBUTING.md](CONTRIBUTING.md).
