# Final Project Rubric

**Navigation:** [Student Projects](README.md) · [Requirements](project_requirements.md) · [Submission Guide](submission_guide.md) · **Rubric**

---

The final project counts for **30%** of the course grade ([assessment policy](../01_course_documentation/course/assessment_policy.md)). It is graded on the [course grading scale](../01_course_documentation/course/assessment_policy.md#3-grading-scale).

## 1. Criteria and Weights

| Criterion                     | Weight   | Excellent (90–100%)                                                                 | Satisfactory (60–79%)                                   | Needs improvement (< 60%)                    |
| ----------------------------- | -------: | ----------------------------------------------------------------------------------- | ------------------------------------------------------- | -------------------------------------------- |
| System architecture           | 15%      | Clear node/topic/TF diagram; justified design choices                               | Diagram present; some choices unexplained               | No architecture description                  |
| ROS 2 implementation          | 20%      | Clean packages, correct use of topics/services/actions/parameters/launch            | Works, with some poor structure or hard-coded values     | Does not build or run                        |
| Robot modeling / simulation   | 15%      | Valid URDF/Xacro; correct frames, collision, and inertia; stable simulation         | Model works with minor problems                          | Model invalid or simulation unstable         |
| Navigation / manipulation     | 20%      | Task achieved reliably and measured (success rate, time, accuracy)                  | Task achieved sometimes; little measurement             | Task not achieved                            |
| System integration            | 10%      | All components start together and interact correctly                                | Components work separately; integration partial         | No integration                               |
| Testing & debugging           | 10%      | Systematic tests with recorded results; problems diagnosed with the right tools     | Some testing                                            | No evidence of testing                       |
| Documentation                 | 5%       | Reproducible README; report explains design, results, and limitations              | Incomplete instructions                                 | Missing                                      |
| Presentation                  | 5%       | Clear, on time, answers technical questions confidently                             | Understandable; some questions unanswered               | Unable to explain the work                   |
| **Total**                     | **100%** |                                                                                     |                                                         |                                              |

## 2. Technical Evaluation

During the presentation, students must explain their own implementation. Examples:

* Why did you use this ROS 2 node, topic, service, or action?
* Which topic carries this data? Which node publishes it?
* Why is this TF transform required, and which node publishes it?
* How does the LiDAR support SLAM? How does Nav2 generate motion commands?
* What happens if the robot loses localization?
* How is forward kinematics calculated for your robot? Why can a manipulator encounter a singularity?
* Why did the motion planner select this trajectory?

Answers affect the **Presentation** score and can reduce other criteria if the student cannot explain submitted work.

## 3. Reproducibility

A project should be reproducible by another student or the instructor:

```text
Clone repository → Install dependencies → Build workspace → Launch system → Reproduce demonstration
```

Projects that work only on the student's computer without clear setup instructions lose marks under **Documentation** and **System integration**. See the [submission guide](submission_guide.md) for the required README contents.

## 4. Deductions

| Issue                                                        | Effect                                      |
| ------------------------------------------------------------ | ------------------------------------------- |
| Late submission                                              | As announced by the instructor              |
| `build/`, `install/`, `log/` or large binary files committed | Documentation score reduced                 |
| Copied work without attribution                              | Handled under the [academic integrity rules](../01_course_documentation/course/assessment_policy.md#7-academic-integrity) |
