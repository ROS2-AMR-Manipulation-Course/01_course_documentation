# Week 2 Practical Assessment — Robot Model & Simulation Demonstration

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Duration:** 15 minutes per student (10 min tasks + 5 min questions)
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** [Week 2 README](../README.md) · [Assessment Overview](assessment.md) · [Quiz](quiz.md) · **Practical Assessment**

---

## 1. Purpose

The practical assessment checks that the student can **run, inspect, explain, and repair** their own robot model. It is not enough for the simulation to work: the student must show **why** it works.

## 2. Before the Assessment (student)

* Your `diffbot_description` package builds without errors from a clean workspace:

```bash
cd ~/ros2_ws
rm -rf build/diffbot_description install/diffbot_description
colcon build --symlink-install --packages-select diffbot_description
source install/setup.bash
```

* All other ROS 2 and Gazebo processes are closed.
* Your hand calculations from Exercises 5 and 6 are available.

---

## 3. Tasks

Students perform the tasks in order. The instructor observes and asks the questions.

### P1 — Validate the model (2 minutes)

```bash
cd ~/ros2_ws/src/diffbot_description/urdf
xacro diffbot.urdf.xacro > /tmp/diffbot.urdf
check_urdf /tmp/diffbot.urdf
gz sdf -p /tmp/diffbot.urdf | grep -E "<link name|<joint name"
```

**Expected:** a valid tree with root `base_footprint`; the SDF shows three links (`base_footprint`, `left_wheel_link`, `right_wheel_link`).

**Ask:** *Why are there seven links in the URDF but only three in the SDF?*

### P2 — TF and forward kinematics (3 minutes)

```bash
ros2 launch diffbot_description display.launch.py
ros2 run tf2_ros tf2_echo base_footprint lidar_link
```

**Expected:** translation (0.100, 0.000, 0.165). The student explains the sum of the three joint origins without looking at notes.

Move a wheel slider. **Ask:** *Which node turns the slider value into a transform? On which topic?*

### P3 — Simulate and drive (3 minutes)

```bash
ros2 launch diffbot_description gazebo.launch.py
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

The student:

1. Drives forward, turns in place, and stops.
2. Shows `/scan` in RViz2 hitting the box and the wall.
3. Shows the TF tree from `odom` to `lidar_link`:

```bash
ros2 run tf2_ros tf2_echo odom lidar_link --ros-args -p use_sim_time:=true
```

**Ask:** *Which node publishes `odom → base_footprint`? How does it reach ROS?*

### P4 — Fault finding (2 minutes)

While the student looks away, the instructor introduces **one** fault from the table below in the student's files. The student must find and fix it using tools, not by comparing files with a reference copy.

| Fault (instructor applies one)                                          | Expected symptom                                                     | Tool the student should use               |
| ----------------------------------------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------- |
| Rename `parent link="chassis_link"` in `lidar_joint` to `chasis_link`    | `robot_state_publisher` exits with a URDF parse error; no robot in RViz2 or Gazebo | `check_urdf` (`parent link [chasis_link] of joint [lidar_joint] not found`) |
| Change the wheel macro `axis` to `0 0 1`                                 | Wheels spin like turntables in RViz2; robot does not drive normally in Gazebo | RViz2 sliders                   |
| Delete the `<xacro:inertial_cylinder>` block from the wheel macro        | Wheels missing in Gazebo; robot does not move                        | `gz sdf -p` warnings                      |
| Change `cmd_vel` `direction` to `GZ_TO_ROS` in `gz_bridge.yaml`           | Teleop has no effect                                                 | Bridge log lines, `ros2 topic info -v /cmd_vel` |
| Delete the `Sensors` system from the world                               | `/scan` exists but has no messages                                   | `ros2 topic hz /scan`, world file         |

**Ask:** *How would you have found this problem on a real robot?*

---

## 4. Oral Questions (choose two, 5 minutes)

1. What is the difference between a `revolute` and a `continuous` joint? When would you use each?
2. Why is the wheel's visual rotated by `rpy="${pi/2} 0 0"` while its joint axis is `0 1 0`?
3. Your robot tips over in Gazebo. Name two properties of the model you would check first.
4. What does `use_sim_time` do, and which nodes in your launch file need it?
5. Your `/odom` says the robot turned 90°, but in Gazebo it did not move. How is that possible?
6. Using `v = r(ωR + ωL)/2` and `ω = r(ωR − ωL)/W`, what wheel speeds does `diffbot` need for v = 0.3 m/s, ω = 0?

**Reference answers for instructors:**

1. `continuous` has no position limits (wheels); `revolute` has lower/upper limits, required by URDF (arm joints).
2. A URDF cylinder's axis is its local z. The visual is rotated so that the cylinder lies along y. The joint frame is not rotated, so the spin axis in the joint frame is y.
3. Centre of mass / mass distribution (inertial origins, `chassis_offset_x`), collision geometry of the wheels and caster, friction (`mu1`/`mu2`), and inertia values.
4. It makes the node use the `/clock` topic (simulation time) instead of the wall clock. In this lab: `robot_state_publisher`, `ros_gz_bridge`, RViz2, and any TF tools.
5. Wheel odometry is calculated from wheel rotation. If the robot is stuck or the wheels slip, the wheels turn but the robot does not. Exercise 9B shows this.
6. ωL = ωR = v / r = 0.3 / 0.05 = 6 rad/s.

---

## 5. Rubric

| Criterion                     | 4 — Excellent                                                       | 3 — Good                                            | 2 — Satisfactory                                     | 1 — Needs improvement                    |
| ----------------------------- | ------------------------------------------------------------------- | --------------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| P1 Validation                 | Runs all commands unaided; explains lumping correctly               | Runs commands; partial explanation                  | Needs help with commands                             | Model does not validate                  |
| P2 TF and FK                  | Correct transform; explains the chain and `robot_state_publisher`   | Correct transform; partial explanation              | Transform shown, cannot explain it                   | Cannot show the transform                |
| P3 Simulation                 | Drives smoothly; scan and full TF tree shown; explains bridge and DiffDrive | Simulation works; one item missing or unexplained | Simulation works with help                         | Simulation does not work                 |
| P4 Fault finding              | Finds and fixes the fault within 2 min using the right tool         | Finds and fixes with a hint                         | Finds but cannot fix                                 | Cannot find the fault                    |
| Oral questions                | Both answers correct and precise                                    | Both mostly correct                                 | One correct                                          | Neither correct                          |

**Score:** total points out of 20 → percentage (× 5). This percentage is the "Demonstration" criterion in the [lab rubric](../lab/exercises.md#grading-rubric-week-2-lab) and counts towards the course's Practical Demonstrations component.

---

## 6. Instructor Notes

* Prepare the fault list in advance and vary the fault between neighbouring students.
* Restore the student's files after P4, or ask the student to keep their fixed version.
* Allow students to use the [Lab Guide troubleshooting section](../lab/lab_guide.md#11-troubleshooting) during P4. The goal is to choose the right tool, not to memorize error messages.
* If the Gazebo GUI cannot render on a student's machine (virtual machine without 3D acceleration), run Gazebo **server-only** with software rendering and use RViz2 to observe the robot. Note this on the score sheet:

  ```bash
  export LIBGL_ALWAYS_SOFTWARE=1
  ros2 launch diffbot_description gazebo.launch.py \
    world:="$(ros2 pkg prefix --share diffbot_description)/worlds/diffbot_world.sdf -s --headless-rendering"
  ```

  This works because the `world` argument is passed into Gazebo's command line (`gz_args`). `-s` starts only the server, and `--headless-rendering` lets the LiDAR render without a window. Course preparation used this setup with Mesa software rendering.
