The pipeline proves the change once, where each proof is cheapest: each task's own test ran as you built; CI runs the gate on the push; Software Validation runs the whole check set once, with counts against the parent commit, and the live rows code-design.md names. Do not run those here.

1. **Write the test plan for Validation.** Copy the Verification section of code-design.md into the pull request's test plan: every command, and every live row with what to call, what to compare and what to expect, each as an unchecked box. Add anything the build changed about what must be proven (a new file, a dropped row, a behavior change in tests). Counts are filled in by Validation; leave none you did not see.
2. **Write down what no one here can run** (a sign-in on staging, a device, a backfill in production, an apply) as an unchecked item with exact steps for the person and the condition that proves it worked: "Staging: sign-in round trip ... not testable locally with a fake tenant". Never report it as passing. Never use the person's own accounts or data. On hardware, the people who own the machine decide what counts as tested.
3. **Check the gate.** What CI enforces varies more than a repository's stated rules suggest: full pre-commit and suites in one, lint only in another, a build only in a third. When the tests that cover the change do not run in CI, say so in the test plan, and add the CI step in its own commit when it is one job with no new secret. When it needs credentials or a cluster, say what blocks it. When node-level or end-to-end behavior is not covered at all, because covering it needs more involved test infrastructure, say exactly what is unproven.
4. **A test you made red stays your problem.** When a task's test, or a test it changed, is red at the head you will push, fix it now. Never make a check pass by loosening an assertion, skipping a test or regenerating an expected output, unless the expected behavior really changed, and then say so. When speed or cost is the point of the change, measure before and after, and state what the fix does not solve.

### Questions to ask here

- Which command proves each row, and who runs it: CI, Validation or the person?
- What can nobody run here? Leave the box unchecked, with the steps.
- Does CI run the tests that cover this change? If not, is adding the step one job with no secret?
- Is any test you touched red right now?
