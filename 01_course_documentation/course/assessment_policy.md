# Assessment Policy

**Navigation:** [Course README](README.md) · [Syllabus](course_syllabus.md) · [Learning Outcomes](learning_outcomes.md) · [Software](software_requirements.md) · **Assessment**

---

## 1. Philosophy

Assessment covers both theoretical understanding and practical robotics skills. Students must show that they can:

* Explain robotics concepts.
* Implement ROS 2 systems.
* Run simulations and analyze robot behavior.
* Debug problems systematically.
* Document their work so that others can reproduce it.

A system that "works" is not enough: students must be able to explain **why** it works.

---

## 2. Components

| Component                      | Weight   | Where it is defined                                             |
| ------------------------------ | -------: | --------------------------------------------------------------- |
| Weekly laboratory work         | 30%      | Each week's `lab/exercise.md` (submission and lab rubric)       |
| Weekly exercises / quizzes     | 20%      | Questions in each week's exercises and lecture review quiz       |
| Practical demonstrations       | 20%      | Demonstration section of each week's `lab/exercise.md`          |
| Final project                  | 30%      | [03_student_projects](../../03_student_projects/README.md)      |
| **Total**                      | **100%** |                                                                 |

### Weekly demonstrations

| Week | Demonstration                                                        |
| ---- | -------------------------------------------------------------------- |
| 1    | ROS 2 node communication and a launch-file system                    |
| 2    | Robot model validation, TF/forward kinematics, and simulation        |
| 3    | Mapping and autonomous navigation (when the lab is published)        |
| 4    | Manipulator motion planning (when the lab is published)              |

Assessment details for weeks whose labs are not yet developed will be published with those labs.

---

## 3. Grading Scale

| Score  | Level             | Description                                          |
| -----: | ----------------- | ---------------------------------------------------- |
| 90–100 | Excellent         | Excellent technical understanding and implementation |
| 80–89  | Very Good         | Strong understanding with minor issues               |
| 70–79  | Good              | Good understanding with some technical limitations   |
| 60–69  | Satisfactory      | Basic requirements achieved                          |
| 50–59  | Needs Improvement | Significant problems but partial achievement         |
| < 50   | Unsatisfactory    | Major requirements not achieved                      |

---

## 4. Laboratory Rubric (all weeks)

Each week's `exercise.md` applies these criteria with week-specific weights:

| Criterion           | Excellent             | Good                   | Needs Improvement         |
| ------------------- | --------------------- | ---------------------- | ------------------------- |
| Implementation      | Fully working         | Mostly working         | Significant issues        |
| ROS 2 understanding | Clearly explained     | Basic explanation      | Limited understanding     |
| Debugging           | Systematic            | Some debugging ability | Unable to diagnose issues |
| Documentation       | Clear and complete    | Adequate               | Incomplete                |
| Demonstration       | Confident and correct | Minor problems         | Unable to demonstrate     |

---

## 5. Technical Questioning

During demonstrations, instructors ask students to explain their own work, for example:

* Why did you use this node, topic, service, or action?
* Which topic carries this data, and which node publishes it?
* Why is this TF transform required, and who publishes it?
* How does the LiDAR data support SLAM? How does Nav2 produce velocity commands?
* What happens if the robot loses localization?
* How is forward kinematics calculated for this robot? Why can a manipulator reach a singularity?
* Why did the motion planner select this trajectory?

Being unable to explain submitted work reduces the Implementation and Demonstration scores.

---

## 6. Submission Principles

Detailed instructions: [Submission Guide](../../03_student_projects/submission_guide.md).

* Submit through a Git repository unless the instructor allows another format.
* Include source code, configuration files, launch files, and documentation; do **not** include `build/`, `install/`, or `log/`.
* Include the evidence each exercise asks for (screenshots, command output, tables).
* Work must be **reproducible**: another person should be able to clone, build, launch, and reproduce the demonstration. Work that runs only on the author's computer loses marks for documentation and reproducibility.
* Deadlines are set by the instructor for each week.

---

## 7. Academic Integrity

Students must:

* Write their own code and reports, and understand everything they submit.
* Reference external resources, including open-source packages and AI assistance where the instructor permits it.
* Document modifications made to existing open-source projects.
* Not submit copied work.

Using open-source ROS 2 packages is allowed when the assignment permits it, but students must understand how the packages are used and configured.
