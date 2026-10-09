# Week 2 Assessment — Kinematic Modeling & Physics Simulation

> **Course:** ROS 2 AMR & Manipulation
> **Week:** 2
> **Environment:** Ubuntu 24.04 · ROS 2 Jazzy · Gazebo Harmonic

**Navigation:** [Week 2 README](../README.md) · **Assessment Overview** · [Quiz](quiz.md) · [Practical Assessment](practical_assessment.md) · [Lab Exercises](../lab/exercises.md)

---

## 1. Purpose

The Week 2 assessment checks that students can:

* **Explain** robot structure: links, joints, DoF, frames, transforms, and forward kinematics.
* **Build** a valid URDF/Xacro model with correct joint origins, axes, collision, and inertial properties.
* **Run** the model in Gazebo Harmonic and verify it with ROS 2 and Gazebo tools.
* **Diagnose** common modeling and simulation problems.

It follows the [course assessment philosophy](../../../course/assessment.md): both theoretical understanding and practical skills are assessed.

---

## 2. Assessment Items

| Item                       | What                                                          | When                    | Course component                 |
| -------------------------- | ------------------------------------------------------------- | ----------------------- | -------------------------------- |
| Lab work                   | `diffbot_description` package + `week02_report.md` ([submission](../lab/exercises.md#submission)) | Due before Week 3 lab   | Weekly Laboratory Work (30% of course) |
| Quiz                       | 10 questions, 25 minutes ([quiz.md](quiz.md))                 | Start of Week 3 lecture, or end of Week 2 lecture | Weekly Exercises / Assignments (20% of course) |
| Practical demonstration    | 15-minute live demonstration + fault-finding ([practical_assessment.md](practical_assessment.md)) | Last hour of the Week 2 lab | Practical Demonstrations (20% of course) |

The percentages are the course-level weights from [course/assessment.md](../../../course/assessment.md). Week 2 is one of four weeks contributing to each component.

---

## 3. Learning Objectives Covered

| Learning objective                                         | Lab exercises | Quiz questions | Practical task |
| ---------------------------------------------------------- | ------------- | -------------- | -------------- |
| Identify links, joints, DoF                                | 1, 2          | 1, 2           | P1, Q&A        |
| Define coordinate frames                                   | 1, 3          | 3              | P2             |
| Explain and compose transformations                        | 6             | 6              | P2             |
| Forward kinematics                                         | 6, 8          | 6              | P2, Q&A        |
| Create a URDF/Xacro description                            | 2, 3, 4       | 4              | P1             |
| Collision and inertial properties                          | 5, 9          | 4, 5, 7        | P4             |
| URDF ↔ TF2 relationship                                    | 6             | 8, 9           | P2             |
| Configure the robot for Gazebo Harmonic, add a sensor      | 7, 10         | 10             | P3             |
| Run the robot in simulation and verify its behavior        | 7, 8          | 9              | P3             |
| Diagnose modeling and simulation problems                  | 9, 11         | 7, 9           | P4             |

---

## 4. Lab Work Rubric

The full rubric is in [exercises.md — Grading Rubric](../lab/exercises.md#grading-rubric-week-2-lab). Summary:

| Criterion            | Weight |
| -------------------- | -----: |
| Implementation       | 35%    |
| Kinematics & TF2     | 20%    |
| Physics & Debugging  | 20%    |
| Documentation        | 10%    |
| Demonstration        | 15%    |

The lab "Demonstration" criterion uses the score from the [practical assessment](practical_assessment.md).

---

## 5. Quiz

* 10 questions, 1 point each, 25 minutes. A calculator is allowed; notes are not.
* Student version: [quiz.md](quiz.md). It contains questions only.
* Instructor answer key with explanations: [quiz_answer_key.md](quiz_answer_key.md). **Instructor only.** Move it to a private location if students can read this repository.

---

## 6. Performance Levels

Consistent with the [course grading scale](../../../course/grading_rubric.md):

| Level        | Score   | Week 2 evidence                                                                                     |
| ------------ | ------- | --------------------------------------------------------------------------------------------------- |
| Excellent    | 90–100  | Model validates and simulates correctly; calculations verified with TF2; physics effects explained with correct reasoning; faults found quickly with the right tools |
| Very Good    | 80–89   | Everything works; minor gaps in explanations or verification                                        |
| Good         | 70–79   | Model works in simulation; some calculations or explanations incomplete                             |
| Satisfactory | 60–69   | Model validates and shows in RViz2; simulation works only with help                                 |
| Needs Improvement | 50–59 | Model partially valid; simulation not working                                                  |
| Unsatisfactory | < 50  | Model does not validate                                                                             |

---

## 7. Academic Integrity

* All students build the same reference robot. The report, hand calculations, experiment explanations, and the practical demonstration must be **your own work**.
* You may discuss problems with classmates. Do not copy their files or reports.
* In the practical assessment, students must explain their own files. Being unable to explain submitted work reduces the Implementation and Demonstration scores.
