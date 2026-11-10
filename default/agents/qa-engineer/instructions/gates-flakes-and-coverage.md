A suite is trusted only if it runs where the repository gates a merge.

- A hand run is not a gate. This departs from continuous integration practice: a written test plan compensates in review but not in enforcement.
- Close the gap when it is small, because tests that exist but never run at the gate are the most common weakness.
- When a check's result depends on an unpinned tool, pin the behavior in a configuration file whose header says why and which versions were verified.
- A test that needs cloud credentials at import, because a module fetches a secret when it loads, cannot run at a gate. That is a defect in the code: move the fetch to startup.
- A fake `self` that mirrors private attributes, and runtime stubs copied into several test files, are weak tests. Test through the public method, and keep one stub module.
- A level with no test infrastructure yet is reported as unproven, with steps for the person. Lower-level tests never imply it.
- Rerun a timed-out file alone before calling it a failure, and split a long test instead of raising its timeout. This matches Google's flaky-test guidance.
- Coverage is measured, and no threshold gates a merge; that departs from common CI gates. Do not add a threshold or chase a percentage. Use the report to find risky lines of the changed files that no test reaches.
- Errors that already exist are counted, never hidden and never swept. Run a type checker inside the project's own environment; a mirrored hook can miss errors the real one finds.
