# Week 2 Exercises

## From a Robot Specification to a Simulated Mobile Robot

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Total practical time:** About 8 hours, including a break and final demonstration
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** [Lab README](README.md) · [Lab Guide](lab_guide.md) · **Exercises** · [Lecture Notes](../lecture/notes.md) · [Kinematics Examples](../lecture/kinematics_examples.md)

---

## How to Use This File

The [Lab Guide](lab_guide.md) shows you **how** to build the robot. These exercises ask you to **check, change, and explain** it. Each exercise has:

* **Core tasks** — required for everyone.
* **Expected outcome** — what you should observe if your work is correct. Use it to check yourself.
* **Questions** — answer them in `week02_report.md` (see [Submission](#submission)).
* **Checkpoint** — show it working before you move on.
* **Extension** — optional, for students who finish early.

**Rules for every exercise:**

1. Source ROS 2 and your workspace in **every** new terminal.
2. After every model change: `xacro … > /tmp/x.urdf && check_urdf /tmp/x.urdf`.
3. Commit your package to Git after each exercise. Use a clear commit message.
4. Record errors and how you solved them. They are part of your report.
5. When an exercise asks you to break something on purpose, **restore the working version** before you continue.

---

## Exercise 1 — Robot Specification, Frames and DoF

**Time:** 20 minutes · **Lecture:** Parts 1–2 · **Lab Guide:** Section 4

### Core tasks

1. On paper, draw a **side view** and a **top view** of `diffbot` using the specification in Lab Guide Section 4. Mark `base_footprint`, `base_link`, `chassis_link`, both wheel frames, `caster_link`, and `lidar_link` with their x (red), y (green), and z (blue) axes.
2. Complete this table from the specification (before you write any URDF):

| Joint             | Type | Parent | Child | `origin xyz` | `axis` |
| ----------------- | ---- | ------ | ----- | ------------ | ------ |
| `base_joint`      |      |        |       |              | —      |
| `chassis_joint`   |      |        |       |              | —      |
| `left_wheel_joint`  |    |        |       |              |        |
| `right_wheel_joint` |    |        |       |              |        |
| `caster_joint`    |      |        |       |              | —      |
| `lidar_joint`     |      |        |       |              | —      |

3. Count the joint DoF of `diffbot`, and the DoF of its base pose in the plane.

### Expected outcome

Your table matches the joints in the final `diffbot.urdf.xacro` (with numbers instead of Xacro expressions). The robot has 2 joint DoF; its base pose has 3 DoF (x, y, θ).

### Questions

1. Why is `base_link` placed at the centre of the wheel axle and not at the centre of the chassis box?
2. Why does `base_footprint` exist? (Hint: REP 105.)
3. The base pose has 3 DoF but only 2 motors. What does *non-holonomic* mean for how `diffbot` can move?

**Checkpoint:** Your drawing and table are complete and checked by a classmate.

---

## Exercise 2 — Write and Validate the First URDF

**Time:** 35 minutes · **Lab Guide:** Sections 3–4

### Core tasks

1. Create the `diffbot_description` package and `urdf/diffbot_basic.urdf`.
2. Validate it with `check_urdf` and draw it with `urdf_to_graphviz`.
3. **Break it on purpose**, one error at a time. Run `check_urdf` after each change, record the **exact error message**, then fix it:

| # | Change                                                    | Error message you saw |
| - | --------------------------------------------------------- | --------------------- |
| a | Rename `parent link="base_link"` in `caster_joint` to `base_lnk` |                |
| b | Change one `</joint>` to `</join>`                        |                       |
| c | Delete the whole `caster_joint` block                     |                       |
| d | Give `left_wheel_joint` a second parent by copying it and naming the copy `left_wheel_joint2` (same child) |  |

### Expected outcome

* The valid file prints a tree with root `base_footprint` and four children of `base_link`.
* (a) `Failed to build tree: parent link [base_lnk] of joint [caster_joint] not found.`
* (b) `Error=XML_ERROR_MISMATCHED_ELEMENT … Line number=…`
* (c) `Failed to find root link: Two root links found: [base_footprint] and [caster_link]`
* (d) **`check_urdf` still reports success.** A link with two parent joints is invalid in URDF, but this validator does not catch it. Validation tools catch many mistakes, not all of them.

### Questions

1. Why did (c) produce *two root links*?
2. Why must a URDF be a tree? Why is (d) still a bug even though `check_urdf` passes?
3. Which of these errors would you **not** notice just by looking at the robot in RViz2?

**Checkpoint:** `check_urdf diffbot_basic.urdf` passes again and you have recorded four error messages.

---

## Exercise 3 — RViz2, Joint Sliders and Joint Axes

**Time:** 30 minutes · **Lab Guide:** Section 5

### Core tasks

1. Launch `display.launch.py` with the Stage 1 model and move both wheel sliders.
2. Change `left_wheel_joint` to `<axis xyz="0 0 1"/>`, restart the launch file, and move the slider. Restore it.
3. Change the left wheel **visual** `rpy` to `0 0 0`, restart, and observe. Restore it.
4. With the sliders at 0, use `tf2_echo` to measure `base_link → left_wheel_link` and `base_link → caster_link`.

### Expected outcome

* Step 1: each wheel rolls about the green y axis.
* Step 2: the wheel spins about the blue z axis, like a turntable.
* Step 3: the wheel appears as a flat disc lying on its side, but its **joint still rolls about y**. The visual origin changes only the drawing, not the motion.
* Step 4: `[0.000, 0.160, 0.000]` and `[0.180, 0.000, -0.025]`.

### Questions

1. What is the difference between a joint `<origin>` and a visual `<origin>`? Use your observations from steps 2 and 3.
2. In which frame is the joint `<axis>` expressed?

**Checkpoint:** You can explain, with screenshots, why steps 2 and 3 look wrong in different ways.

---

## Exercise 4 — Xacro and Parametric Design

**Time:** 45 minutes · **Lab Guide:** Section 6

### Core tasks

1. Create `diffbot.urdf.xacro` (Stage 2) and check it with `xacro` + `check_urdf`.
2. Find every place where `wheel_radius` is used in the generated URDF (`grep` the output).
3. **Change the design:** set `wheel_radius` to `0.07` and `wheel_offset_y` to `0.18`. Regenerate the URDF and check, with `display.launch.py` and `tf2_echo`:
   * `base_footprint → base_link`
   * `base_footprint → caster_link` (is the caster still on the ground?)
4. **Write a macro:** turn the caster into a macro `caster_wheel` with parameters `prefix` and `offset_x`, and use it once for the front caster (`prefix="caster"`, `offset_x="${caster_offset_x}"`). The generated URDF must still contain a link named `caster_link`.
5. Restore `wheel_radius = 0.05` and `wheel_offset_y = 0.16`.

### Expected outcome

* Step 3: `base_footprint → base_link` = (0, 0, **0.07**); `base_footprint → caster_link` z = **0.025** (= caster radius), so the caster still touches the ground. The wheels move outward to y = ±0.18.
* Step 4: `check_urdf` prints the same tree as before.

### Questions

1. Which `origin` values updated automatically in step 3, and why?
2. List two advantages of macros, using your wheel and caster macros as examples.
3. Why should the Gazebo diff-drive plugin use `${wheel_radius}` instead of the number `0.05`?

**Checkpoint:** Your Xacro model works with both wheel sizes, and the caster macro produces `caster_link`.

**Extension:** Add a `use_lidar` Xacro argument (`<xacro:arg name="use_lidar" default="true"/>` with `<xacro:if value="$(arg use_lidar)">`) and test `xacro diffbot.urdf.xacro use_lidar:=false`.

---

## Exercise 5 — Inertial Properties

**Time:** 30 minutes · **Lab Guide:** Section 7 · **Lecture:** [Kinematics Examples, Example 6](../lecture/kinematics_examples.md#example-6--inertia-of-the-lab-robot)

### Core tasks

1. Calculate **by hand** the mass and I_xx, I_yy, I_zz of the chassis, one wheel, and the caster. Show your working.
2. Add the inertial macros (Lab Guide Section 7) and compare the generated `<inertia>` values with your calculation.
3. Calculate the total mass of the robot.
4. Run `gz sdf -p /tmp/diffbot.urdf` and find the **single** `<inertial>` block of the lumped `base_footprint` link. Record its `<mass>`.

### Expected outcome

* Your hand values match the generated values (chassis I_zz = 0.037933 kg·m², wheel I_zz = 0.000375 kg·m² before rotation).
* Total mass: 2.8 kg after Section 8 (2.7 kg before the LiDAR is added).
* The lumped `base_footprint` mass is **2.2 kg** = chassis 2.0 + caster 0.1 + LiDAR 0.1 (after Section 8). The wheels stay separate links.

### Questions

1. Why is the wheel's inertial `origin` rotated by `rpy="${pi/2} 0 0"`?
2. Why do `base_footprint` and `base_link` have no `<inertial>` in the URDF, but a mass in the SDF?
3. What would happen in Gazebo if a wheel had `mass="0.0"`?

**Checkpoint:** Your hand calculation and the generated values agree.

---

## Exercise 6 — TF2 and Forward Kinematics

**Time:** 45 minutes · **Lab Guide:** Section 8 · **Lecture:** [Kinematics Examples, Examples 3–4](../lecture/kinematics_examples.md#example-3--chaining-transforms-in-the-lab-robot)

### Core tasks

1. Add the LiDAR (Lab Guide Section 8). Generate the TF tree PDF with `view_frames`.
2. Calculate `base_footprint → lidar_link` by hand, then check it with `tf2_echo`.
3. Create `two_link_arm.urdf.xacro` from [Kinematics Examples 4.1](../lecture/kinematics_examples.md#41-the-arm-as-a-urdf). For each configuration below, calculate (x, y) by hand, then check with `robot_state_publisher` + `tf2_echo base_link tool0`:

| θ1 (rad)  | θ2 (rad)  | Your hand (x, y) | `tf2_echo` (x, y) |
| --------: | --------: | ---------------- | ----------------- |
| 0.5236    | 1.0472    |                  |                   |
| 1.5708    | −1.5708   |                  |                   |
| 0.7854    | 0.7854    |                  |                   |

4. Try `joint2 = 2.0` rad. Use `joint_state_publisher_gui` and check how far the slider lets you go.

### Expected outcome

* LiDAR: (0.100, 0.000, 0.165).
* Arm: (0.433, 0.550), (0.300, 0.500), (0.354, 0.654), all with z = 0.100.
* Step 4: the slider stops at ±1.57 rad (±90°), the `<limit>` of `joint2`.

### Questions

1. Which node computes forward kinematics in ROS 2? What are its inputs and outputs?
2. Why must `ros2 topic pub` on `/joint_states` include `header: {stamp: now}`?
3. Which transforms are on `/tf_static`, and which are on `/tf`? Why?

**Checkpoint:** All hand calculations match `tf2_echo` to 3 decimal places.

**Extension:** Add a prismatic joint `joint3` (axis z, `lower="0" upper="0.2"`) between `link2` and `tool0`. Verify that θ1 = 30°, θ2 = 60°, d = 0.15 m gives (0.433, 0.550, 0.250).

---

## Exercise 7 — Launch and Drive in Gazebo Harmonic

**Time:** 50 minutes · **Lab Guide:** Sections 9–10.3

### Core tasks

1. Complete Lab Guide Section 9 and convert the model with `gz sdf -p`. Record which links remain in the SDF.
2. Launch `gazebo.launch.py`. Take a screenshot showing **both** Gazebo and RViz2.
3. Run `ros2 topic list`, `gz topic -l`, and `gz model --list`. Record which topics exist on **both** sides of the bridge.
4. Drive the robot with `teleop_twist_keyboard`:
   * forward 1 m and back,
   * a full turn in place,
   * an approximate 1 m × 1 m square.
5. Drive until the robot touches the green box. Describe what happens.

### Expected outcome

* SDF links: `base_footprint`, `left_wheel_link`, `right_wheel_link`.
* `/cmd_vel`, `/odom`, `/tf`, `/joint_states`, `/scan`, `/clock` appear in both `ros2 topic list` and `gz topic -l`.
* The robot drives straight for pure `linear.x`, turns counter-clockwise for positive `angular.z`, and is stopped by the box. It does not pass through it.

### Questions

1. Why do only three links appear in the SDF, while TF shows seven frames?
2. What does each of the four systems in the world file do?
3. What is the role of `ros_gz_bridge`? Which topic goes from ROS to Gazebo?

**Checkpoint:** One command starts the full simulation, and you can drive the robot with the keyboard.

---

## Exercise 8 — Check Odometry Against the Kinematic Model

**Time:** 30 minutes · **Lab Guide:** Sections 10.4–10.6 · **Lecture:** [Kinematics Examples, Example 5](../lecture/kinematics_examples.md#example-5--differential-drive-kinematics)

### Core tasks

1. Record `/odom` position and `/joint_states` positions.
2. Publish `linear.x = 0.2` for about 5 seconds, then stop.
3. Record both again. Calculate:
   * distance travelled `d` from `/odom`,
   * change in wheel angle `Δθ` for each wheel,
   * estimated wheel radius `r_est = d / Δθ`.
4. Turn in place with `angular.z = 0.5` until `/odom` reports about 90°. Compare the left and right wheel angle changes.
5. Compare the odometry pose with Gazebo's true pose: `gz model -m diffbot -p`.

### Expected outcome

* `r_est` ≈ 0.05 m.
* In step 4 the wheels turn by equal amounts in **opposite** directions.
* `/odom` and the Gazebo pose are close on flat ground, but not identical.

### Questions

1. Why is the measured distance slightly less than 0.2 m/s × 5 s?
2. Odometry is calculated from wheel rotation. Give two situations where it would be wrong on a real robot.
3. Use the formula `ω = r (ωR − ωL) / W` to predict the wheel speeds for `angular.z = 0.5`. Compare with your measurement.

**Checkpoint:** Your `r_est` is within 5% of 0.05 m.

---

## Exercise 9 — Physics Experiments: Friction, Mass and Collision

**Time:** 45 minutes · **Lecture:** Sections 11–12 · **Lab Guide:** Section 11.4

Do each experiment, record what you see, then **restore the original value**. Use the same test each time: from the start position, publish `angular.z = 0.5` for 3 seconds, then stop. Compare the result with the unchanged robot.

| # | Change                                                                 | Check with                                        |
| - | ---------------------------------------------------------------------- | ------------------------------------------------- |
| A | Caster friction `mu1`/`mu2` from `0.001` to `1.0`                      | Turn test; `gz model -m diffbot -p`               |
| B | `chassis_offset_x` from `0.05` to `-0.15` (centre of mass behind the wheels) | Gazebo view; `gz model -m diffbot -p` (pitch); turn test; `/odom` |
| C | Remove the `<xacro:inertial_cylinder>` from the wheel macro            | `gz sdf -p` warnings; spawn and look at the robot |
| D | Remove the `<collision>` from the wheel macro                          | Spawn and look at the robot                       |

### Expected outcome

In the course test environment, the 3-second turn test gave:

| Experiment       | Observed                                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------------------- |
| Unchanged robot  | Turns about 90° (true pose ≈ 1.6 rad)                                                                   |
| A                | Turns only about 60°, and the turning centre drifts a few centimetres. The caster drags                 |
| B                | Robot tips backward about 6–7° (pitch ≈ −0.11 rad) onto the chassis. When commanded to turn, the **true pose does not change**, but **`/odom` still reports about 80° of turning** |
| C                | `gz sdf` warns `link[left_wheel_link] has no <inertial> block defined` … `is not modeled in sdf`. The wheels are missing in Gazebo |
| D                | Record your own observation                                                                             |

Your numbers can differ a little. The direction of each effect should be the same.

### Questions

1. Why did `/odom` in experiment B report a turn that did not happen? What does this tell you about trusting wheel odometry?
2. Why is the caster given almost zero friction?
3. Which of these problems would `check_urdf` detect? Which would only show up in simulation?

**Checkpoint:** Your table has an observation and an explanation for each experiment, and the original model works again.

---

## Exercise 10 — Configure the LiDAR

**Time:** 30 minutes · **Lecture:** Section 14 · **Lab Guide:** Sections 9.1, 10.4

### Core tasks

1. With the original model, record from `/scan`: the number of ranges, `range_max`, `angle_increment`, and the range straight ahead.
2. Change the sensor to `<samples>180</samples>` and `<max>1.8</max>` in `diffbot.gazebo.xacro`. Stop and relaunch the simulation (with `--symlink-install` no rebuild is needed; the launch file runs `xacro` each time). Record the same values.
3. Count how many ranges are finite (not `inf`) in both cases.
4. Measure the rate of `/scan` with `ros2 topic hz`, then change `<update_rate>` to `5` and measure again.
5. Restore the original sensor settings.

Useful commands:

```bash
ros2 topic echo /scan --once --field range_max
ros2 topic echo /scan --once --field angle_increment
ros2 topic echo /scan --once --field ranges
```

### Expected outcome

* Original: 360 ranges, `range_max` 8.0, the box at about 1.65 m straight ahead, the wall at about 1.95 m on the left.
* Modified: 180 ranges, `range_max` 1.8. Only the box (1.65 m) is detected. The wall (1.95 m) is now beyond the range and reads `inf`.
* The measured rate drops when `update_rate` is reduced.

### Questions

1. What is the angular resolution (in degrees) with 360 and with 180 samples?
2. Why is the scan's `frame_id` important for SLAM and navigation in Week 3?
3. What happens to the scan if the `Sensors` system is removed from the world?

**Checkpoint:** Your measurements for both configurations are recorded, and the original settings are restored.

---

## Exercise 11 — Debugging Challenge

**Time:** 40 minutes · **Lab Guide:** Section 11

The files below contain **five bugs**. Copy your working package to a new folder first, for example `cp -r diffbot_description ~/debug_diffbot_description`. Then apply the changes in the copy (rename the package in `package.xml` and `CMakeLists.txt` to `debug_diffbot_description`), or ask your instructor for the prepared broken package.

```xml
<!-- diffbot.urdf.xacro (excerpt) -->
<joint name="${prefix}_wheel_joint" type="revolute">
  <parent link="base_link"/>
  <child link="${prefix}_wheel_link"/>
  <origin xyz="0 ${reflect * wheel_offset_y} 0" rpy="0 0 0"/>
  <axis xyz="0 0 1"/>
</joint>

<joint name="lidar_joint" type="fixed">
  <parent link="chasis_link"/>
  <child link="lidar_link"/>
  ...
</joint>
```

```yaml
# gz_bridge.yaml (excerpt)
- ros_topic_name: "cmd_vel"
  gz_topic_name: "cmd_vel"
  ros_type_name: "geometry_msgs/msg/Twist"
  gz_type_name: "gz.msgs.Twist"
  direction: GZ_TO_ROS
```

```xml
<!-- diffbot_world.sdf (excerpt): the Sensors system block has been deleted -->
```

### Core tasks

For each bug, record:

| # | Symptom you observed | Tool / command that revealed it | Fix |
| - | -------------------- | ------------------------------- | --- |
| 1 |                      |                                 |     |
| 2 |                      |                                 |     |
| 3 |                      |                                 |     |
| 4 |                      |                                 |     |
| 5 |                      |                                 |     |

### Expected outcome

You find all five bugs. You can name the tool that reveals each one, from this list: `check_urdf`, RViz2 sliders, `gz sdf -p`, `ros2 topic info -v`, `ros2 topic hz`, `gz topic -l`.

**Checkpoint:** The debug copy works again, and your table names a different symptom for each bug.

---

## Exercise 12 (Optional) — Compare with the Trailobot Model

**Time:** Extension · Requires the Trailobot repository provided by your instructor

1. Open the Trailobot description package and draw its TF tree using `xacro`, `check_urdf`, and `view_frames`.
2. Compare it with `diffbot`: base frames, wheel joints, sensor frames, Gazebo plugins, bridge configuration.
3. List three things Trailobot does differently and explain why.

---

## Submission

Submit a link to a Git repository (or a `.zip` if your instructor allows it) named `week02_<student_id>`:

```text
week02_<student_id>/
├── week02_report.md
├── screenshots/
│   ├── ex03_rviz_axes.png
│   ├── ex06_tf_tree.pdf
│   ├── ex07_gazebo_rviz.png
│   ├── ex09_physics.png
│   └── ex10_lidar.png
└── src/
    └── diffbot_description/
        ├── CMakeLists.txt
        ├── package.xml
        ├── config/gz_bridge.yaml
        ├── launch/{display,gazebo}.launch.py
        ├── rviz/{diffbot,diffbot_gazebo}.rviz
        ├── urdf/{diffbot_basic.urdf, diffbot.urdf.xacro, inertial_macros.xacro, diffbot.gazebo.xacro, two_link_arm.urdf.xacro}
        └── worlds/diffbot_world.sdf
```

Do **not** submit the `build/`, `install/`, or `log/` folders.

`week02_report.md` must contain:

* The tables and answers for every exercise.
* Your hand calculations (Exercises 5, 6, 8).
* For Exercise 9: one paragraph per experiment explaining **why** the robot behaved as it did.
* A troubleshooting note describing **one problem you solved**: symptom, cause, tool used, and fix.

### Grading Rubric (Week 2 Lab)

Based on the [course laboratory rubric](../../../course/grading_rubric.md).

| Criterion            | Weight | Excellent (90–100%)                                              | Satisfactory (60–79%)                                  | Needs improvement (< 60%)                    |
| -------------------- | -----: | ---------------------------------------------------------------- | ------------------------------------------------------ | -------------------------------------------- |
| Implementation       | 35%    | Package builds; model validates; simulation, bridge, and LiDAR all work | Model works in RViz2; simulation partly works     | Model does not validate or spawn             |
| Kinematics & TF2     | 20%    | All hand calculations correct and verified with TF2              | Most calculations correct; some not verified           | Calculations missing or incorrect            |
| Physics & Debugging  | 20%    | Every experiment explained with correct physical reasoning; all 5 bugs found | Observations recorded; explanations partial  | Experiments or debugging missing             |
| Documentation        | 10%    | Clean repository, clear report, all screenshots                  | Report complete but unclear in places                  | Report or screenshots missing                |
| Demonstration        | 15%    | Demonstration completed and explained confidently ([practical assessment](../assessment/practical_assessment.md)) | Demonstration completed with help | Demonstration not completed |
| **Total**            | **100%** |                                                                |                                                        |                                              |

Extensions are not required for full marks, but may be used to recover points lost elsewhere (instructor's discretion).

---

## Suggested 8-Hour Schedule

| Time        | Activity                                          |
| ----------- | ------------------------------------------------- |
| 00:00–00:20 | Exercise 1: Specification, frames, DoF            |
| 00:20–00:55 | Exercise 2: First URDF and validation             |
| 00:55–01:25 | Exercise 3: RViz2 and joint axes                  |
| 01:25–02:10 | Exercise 4: Xacro and parametric design           |
| 02:10–02:40 | Exercise 5: Inertial properties                   |
| 02:40–03:25 | Exercise 6: TF2 and forward kinematics            |
| 03:25–03:40 | Break                                             |
| 03:40–04:30 | Exercise 7: Launch and drive in Gazebo            |
| 04:30–05:00 | Exercise 8: Odometry vs. kinematic model          |
| 05:00–05:45 | Exercise 9: Physics experiments                   |
| 05:45–06:15 | Exercise 10: LiDAR configuration                  |
| 06:15–06:55 | Exercise 11: Debugging challenge                  |
| 06:55–08:00 | Integration, report writing, practical assessment |

**Instructor note:** Prioritize Exercises 2, 4, 6, 7, and 9. They cover URDF validation, Xacro, TF2/FK, simulation, and physics. Exercise 10 can be shortened if time is limited. Exercise 12 is optional. For students working in a virtual machine without 3D acceleration, see Lab Guide Section 11.6.
