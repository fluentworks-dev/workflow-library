A proof that only this machine saw does not protect the default branch. Make the checks hold where the repository gates a merge.

1. Read what the gate runs at the current head. List the tests that cover this change, and say for each whether the gate runs it. What a gate enforces varies more than written rules suggest; these are the shapes to look for in any repository:

| What the gate runs | What it does not enforce | What you do |
|---|---|---|
| Pre-commit (the interface description regenerated, lint, format, type check) plus every test suite | Only the live run | Watch it |
| Lint, type check and browser tests on three browsers | Only the live run | Run one browser locally first, then watch it |
| Format checks, offline module tests and a plan | The apply | Write the apply as steps for the person |
| Lint only | The tests and the type check, which exist only locally; the unit tests may need cloud credentials at import | Run them by hand and paste the counts |
| A bundle or build validation only | The pipeline tests, which are run by hand | Run them by hand and paste the counts |
| A build only | Every test; reviewers police the test on the real device | Run the offline tests; leave the device boxes unchecked, with steps |
| No CI at all | Everything; people plan and apply by hand | Run every check by hand, with counts |

A gate is usually added only when a tool's drift breaks something (for example, a lint rule set gets pinned after an upstream default change produces a wave of new findings), and until then the written test plan carries what CI does not. Do better where it is cheap, as step 3 says.

2. When they run only by hand, run them yourself on the updated branch and put the command and the count in the test plan, marked as outside the gate. When the repository has an on-demand check or preview workflow, trigger it on the branch.
3. When adding the existing tests to the gate needs no new secret, credential or paid runner, add the step in its own commit and show one green run. Otherwise write one line for the person: the workflow file, the command, what blocks it.
4. Wait for the pull request's checks with a single watching command, run as its own command (`gh pr checks <number> --watch`, or the host's equivalent). Never a loop of sleeps, and never chained after a push.
5. For a failing check, read the whole output. Prove whether it already fails on the parent commit, and say it in this form: "the 30 failures in `file-picker`, `checkout-form` and `session-warning-dialog` reproduce identically on the unmodified parent commit"; "334 failures, the same list as before this branch's review follow-up"; "Mypy hook skipped: its 2 errors pre-exist on `main` (verified against an unmodified checkout)". Fix what is yours.
6. For a flaky check, read the trace before touching the component: speculative component fixes get reverted once the trace shows CPU starvation. Starvation is fixed in the runner (two workers, coverage on one browser). A long test is split. A timeout is never raised to get green.
7. At most three rounds of fixing. Then stop and report what is still red, with the output.

Mark the pull request ready for review only when its checks are green at the current head and no promise is failing. Otherwise leave it as a draft and say why in the summary. A pull request that is red on purpose, because a stacked pull request carries its tests, says so in its description ("The test suite is red on this PR alone"); validate the stack together.

Before you leave this stage, reread the pull request's test plan against the current head. Every command in it was run at this head or its box is unchecked. Every count is the one you saw. Nothing describes something the branch no longer contains. The lines you add or correct have this shape:

```
- [x] `<exact command>`: <count> (baseline before this branch: <count>)
- [x] <live step>: <what was observed, with numbers>
- [ ] <step that could not be run here>: <why>, <who can run it>

- [x] `poetry run pytest tests/`: 132 passed, 1 skipped (baseline before this branch: 126 passed, 1 skipped)
- [x] Attach routes exercised by direct HTTP and through the generated Python and TypeScript clients: 14/14 each
- [x] `poetry run mypy src/`: 21 errors, all pre-existing (the same list on an unmodified checkout)
- [ ] Staging: upload from the packaged application: needs the signed build; the person
```

More lines in the register: "Playwright, Chromium: 63 passed"; "`terraform test` in all 10 modules: 64/64 pass"; "126 passed, 1 skipped (baseline before this branch: 114 passed, 1 skipped)"; unchecked, "Staging: sign-in round trip ... not testable locally with a fake tenant" and "Test on the device in offline mode"; a style rule as an item, `- [x] No em dashes`, checked only on zero hits.

### Questions to ask here

- Do the tests for this change run at the gate, or only on your machine?
- Red because of us? Show it on the parent commit.
- Flaky, or broken? What does the trace say?
- Did anything get skipped, loosened or given a longer timeout to go green? Put it back.
- Is every line of the test plan still true at this head?
