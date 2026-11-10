Policy differs by repository on every point a merge touches; the defaults in these instructions apply only where the repository is silent. Before changing anything, read: the last twenty commit subjects on the default branch; the pull request template and the automated reviewer's instruction file; whether working documents sit on the default branch; which checks a pull request triggers; the base branch, the merge method and the version rule; the pin lanes in the manifest.

What varies across repositories:

- Body: Changes, Test plan and `Related PRs` by default; the repository's template where one exists (Summary, Additions, Removals, Changes, Deprecation; Motivation, Approach, Result with screenshots; Description with Release Notes for operators and for developers). A template is followed because another team makes its release notes from it.
- Working documents: removed before merge in most; kept where they document long-lived architecture or the README links the plan; banned outright in some.
- What CI gates: from pre-commit plus every test suite, down to lint only, bundle validation only, a build only, or nothing where a person plans and applies.
- Target and versions: the default branch by squash; `develop` and `release` flows with a `VERSION` file that CI checks; a version tag after every merge that deploys production; a version in `pyproject.toml` with a changelog entry.
- Pins: exact SDK versions with a development-build lane; a module repository pinned by SHA; tools pinned exactly in one file.
- Subjects: sentence subjects by default; a prefix convention (`feat(scope):`) where the repository has one.

Rules that follow:

- When CI gates less than the tests that exist, the written test plan carries what CI does not: each ungated command with its count.
- When the repository has a template, fill it and add what it lacks (a Test plan, `Related PRs`).
- When the working-documents policy is unwritten, read it from the default branch: present there means kept.
- When a pattern exists in a sibling repository, port it verbatim and name the port in the commit ("Adopt the sibling service's deploy workflow pattern"). Consistency across the organization beats a local improvement.
