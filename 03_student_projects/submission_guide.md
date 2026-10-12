# Submission Guide

**Navigation:** [Student Projects](README.md) · [Requirements](project_requirements.md) · **Submission Guide** · [Rubric](project_rubric.md)

---

This guide applies to the **final project**. Weekly lab submissions follow the same principles, with the folder structure given in each week's `lab/exercise.md`.

## 1. How to Submit

1. Create a Git repository (GitHub unless the instructor says otherwise). Name it `ros2_course_project_<team_or_student_id>`.
2. Give the instructor read access if the repository is private.
3. Create a Git tag for the submitted version:

```bash
git tag -a submission -m "Final project submission"
git push origin submission
```

4. Submit the repository URL and the tag name through the channel announced by the instructor.

The tagged commit is graded. Commits after the deadline are not considered unless the instructor allows it.

## 2. Repository Structure

```text
ros2_course_project_<id>/
├── README.md                 How to install, build, run, and reproduce the demonstration
├── report/
│   └── report.md (or .pdf)
├── media/
│   ├── demo_video_link.md    Link to the video (do not commit large video files)
│   └── *.png                 Screenshots, TF tree, plots
└── src/
    ├── <your_package_1>/
    └── <your_package_2>/
```

Do **not** commit `build/`, `install/`, `log/`, virtual environments, or large binaries. Use a `.gitignore`.

## 3. Required README Contents

Your `README.md` must let another person reproduce your result without asking you:

1. **Overview**: what the system does, in 3–5 sentences, plus an architecture diagram.
2. **Environment**: Ubuntu 24.04, ROS 2 Jazzy, Gazebo Harmonic, plus any other versions.
3. **Dependencies**: `apt` packages, and external repositories with branch or commit.
4. **Build**: exact commands from an empty workspace.
5. **Run**: exact launch command(s) for the demonstration.
6. **Expected result**: what the reader should see, and how to check it (topics, TF, metrics).
7. **Evaluation**: a summary of the measured results.
8. **Credits and licenses**: external code used.

## 4. Report Contents

| Section         | Contents                                                                 |
| --------------- | ------------------------------------------------------------------------ |
| Introduction    | Problem, goal, chosen track                                              |
| Design          | Architecture (nodes, topics, TF), robot model, key design decisions      |
| Implementation  | Packages, configuration, important parameters                            |
| Evaluation      | Success criteria, test method, results (tables or plots), discussion     |
| Limitations     | What does not work and why; what you would do next                       |
| References      | Documentation and code used                                              |

## 5. Evidence

Include the evidence that supports your claims:

* A `view_frames` PDF or image of the TF tree.
* `rqt_graph` screenshot of the running system.
* Logs or recorded data (e.g. `ros2 bag` summaries) for the evaluation, when relevant.

## 6. Checklist Before Submitting

```text
[ ] Fresh clone builds with the README instructions
[ ] Demonstration starts with the documented launch command
[ ] No build/, install/, log/ or large files committed
[ ] External code credited with version and license
[ ] Report and video link included
[ ] Submission tag pushed
```
