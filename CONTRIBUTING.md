# Contributing

Thank you for helping improve the ROS 2 AMR & Manipulation course. Students, teaching assistants, and instructors are all welcome to report problems and suggest improvements.

## Reporting a Problem

Open a GitHub issue and include:

* The file and section, for example `week02_kinematics_simulation/lab/lab_guide.md`, Section 10.3.
* What you did: the exact commands.
* What you expected, and what happened: the exact error message or a screenshot.
* Your environment: `lsb_release -a`, `echo $ROS_DISTRO`, `gz sim --versions`.

Report problems with **robot code** to the robot's own repository (see [robot platforms](01_course_documentation/course/robot_platforms/README.md)). This repository holds only the course documentation.

## Proposing a Change

1. Create a branch from `main`.
2. Make your change and check it with the rules below.
3. Open a pull request that explains **what** changed and **why**.

## Documentation Rules

| Rule | Why |
| ---- | --- |
| Target **Ubuntu 24.04, ROS 2 Jazzy, Gazebo Harmonic** | Everyone uses the same environment |
| Do not copy Gazebo Classic or ROS 1 instructions | They do not work on the course stack |
| Mark commands as **tested** only if you ran them, and say how (OS, packages, hardware or simulation) | Students rely on the instructions working |
| Never invent package names, launch commands, robot dimensions, or sensor settings | Take them from the source repository or a datasheet, and cite it |
| Use **relative links** inside this repository; use full URLs for external sources | Links keep working when the repository is moved or forked |
| Link to official documentation for the correct version (Jazzy, Harmonic, Nav2 `/jazzy/`) | Version mismatches are the most common source of errors |
| Separate **required** activities from **optional** extensions | Students know what is graded |
| Do not commit build outputs (`build/`, `install/`, `log/`), large binaries, credentials, or personal data | Keeps the repository clean and safe |
| Lecture slides are added by the course maintainer only | Avoids outdated or unofficial slides |

## Weekly Folder Layout

```text
weekNN_<topic>/
├── README.md          overview, objectives, prerequisites, deliverables, references
├── lecture/
│   ├── README.md      lecture topic, objectives, outline, role of the slides
│   └── slides.pdf     added by the course maintainer
└── lab/
    ├── README.md      lab overview, requirements, workflow
    ├── lab_guide.md   step-by-step instructions, expected results, troubleshooting
    └── exercise.md    exercises, questions, evidence, rubric
```

Do not add weekly `assessment/` or `resources/` folders. Put assessment material in `lab/exercise.md` or the lecture README, and put references in the weekly README and [04_references](04_references/README.md).

## Before Opening a Pull Request

* Check that every relative link points to an existing file and heading.
* Proofread headings, tables, and code blocks.
* If you changed lab commands, run them in the course environment and describe the test in the pull request.

## License

By contributing, you agree that your contribution is licensed under the repository's [CC BY 4.0](LICENSE) license.
