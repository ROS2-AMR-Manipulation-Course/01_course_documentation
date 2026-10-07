# Grading Rubric

## 1. General Scale

|  Score | Level             | Description                                          |
| -----: | ----------------- | ---------------------------------------------------- |
| 90–100 | Excellent         | Excellent technical understanding and implementation |
|  80–89 | Very Good         | Strong understanding with minor issues               |
|  70–79 | Good              | Good understanding with some technical limitations   |
|  60–69 | Satisfactory      | Basic requirements achieved                          |
|  50–59 | Needs Improvement | Significant problems but partial achievement         |
|   < 50 | Unsatisfactory    | Major requirements not achieved                      |

## 2. Laboratory Rubric

| Criterion           | Excellent             | Good                   | Needs Improvement         |
| ------------------- | --------------------- | ---------------------- | ------------------------- |
| Implementation      | Fully working         | Mostly working         | Significant issues        |
| ROS 2 Understanding | Clearly explained     | Basic explanation      | Limited understanding     |
| Debugging           | Systematic            | Some debugging ability | Unable to diagnose issues |
| Documentation       | Clear and complete    | Adequate               | Incomplete                |
| Demonstration       | Confident and correct | Minor problems         | Unable to demonstrate     |

## 3. Final Project Rubric

| Criterion                   |   Weight |
| --------------------------- | -------: |
| System Architecture         |      15% |
| ROS 2 Implementation        |      20% |
| Robot Modeling / Simulation |      15% |
| Navigation / Manipulation   |      20% |
| System Integration          |      10% |
| Testing & Debugging         |      10% |
| Documentation               |       5% |
| Presentation                |       5% |
| **Total**                   | **100%** |

## 4. Technical Evaluation

Students should demonstrate that they understand their implementation.

For example, students may be asked:

* Why did you use this ROS 2 node?
* Which topic carries this data?
* Why is this TF transform required?
* How does the LiDAR support SLAM?
* How does Nav2 generate motion commands?
* What happens if the robot loses localization?
* How is forward kinematics calculated?
* Why can a manipulator encounter a singularity?
* Why did the motion planner select this trajectory?

## 5. Reproducibility

A project should be reproducible by another student or instructor.

A good submission should provide:

```text
Clone Repository
      ↓
Install Dependencies
      ↓
Build Workspace
      ↓
Launch System
      ↓
Reproduce Demonstration
```

Projects that work only on the student's computer without clear setup instructions should receive reduced marks for documentation and reproducibility.
