Applies when the repository uses ROS 2 and ships to robots.

- Where the repository uses `develop` for features, `release` for hotfixes, then `main`, check the base first. The `VERSION` file is bumped by the hotfix-versus-minor rule, and CI checks it.
- Follow the repository's template (Description, Release Notes for operators and for developers, Test plan) and its conventional commits. Where it bans working documents, none stays on the branch.
- A new key that selects an environment is required with no code default; the staging value ships in the template; `### Breaking changes` carries the exact TOML; each robot is a step for a person. A tunable defaults to its constant for backward compatibility.
- CI only builds; reviewers police the run on hardware, often approving pending a test on the simulator. Leave unverified boxes unchecked. A check on a robot is a person's step, or a blocker when the reviewers made it a condition.
- The SDK pin in `requirements.txt` is mirrored in `setup.py`; both move together.
- Operator-facing options are kept; an unused one is not dead code.
- Ask about a robot's environment, such as where its network configuration is set; do not guess.
