Fix every blocker, simplification and nit whose fix is clear, on this branch, in the repository's style. That includes a restructure from the design stage and a mechanical change that reaches outside the diff (one helper replacing hand-written copies, a rename across files): touching other files is not by itself a consequence. Do not turn a clear fix into a follow-up, and do not leave it as a suggestion.

In order:

1. Blockers first, then simplifications, then nits. A simplification that deletes code removes the nits that sat on it.
2. Each fix of a defect comes with a test that fails without it. Write the test, see it fail for the right reason, then fix.
3. Keep every changed line traceable to a finding. Make no improvement nobody raised.
4. Fix prose in its own commit: a stale comment, a false sentence, a broken link.
5. Run the test of each fix, red then green. The type check, the lint and the gate set are not run here: CI runs them on the push and Software Validation runs them once with counts. Report only what you ran.
6. Commit: one commit per review round, or one per substantive fix when the fixes are unrelated. Tests for the fixes may have their own commit. The subject names the round and enumerates the points; where the repository's convention puts a ticket key in the subject, add it.
7. Push without force. Review fixes are new commits on top: never amend, never fold.
8. Reply in each thread you fixed with the commit and the covering test, then resolve it.
9. Reread the description against the branch as it now is, and correct it: the Changes that changed, the test plan's commands and rows (counts stay Validation's), a Decided for you line for each choice you made. Leave the draft or ready state as it is: validation gates on CI.

After three failed attempts at one fix, stop adding fixes and question the approach.

### Before you push, read your own fix as a stranger

This session has now written code, so its diff gets the same questions:

- Is any change here not relevant to the ticket? Does every changed line trace to a finding?
- What is the point of the changes in each file? Are they necessary, or a workaround? Did a fix add a file, a helper, an option or a parameter? Could it have deleted code instead?
- Is each piece required, or optional and better simplified for now?
- Does anything need to be stored and committed now? Is any debug output, scratch file, log or generated file in the diff?
- Does every comment, docstring and message I wrote say what the code now does? None narrates the fix or defends a reversal.
- Do new names use full words, carry their unit and match the names in the designs?
- Did I see each new test fail without its fix?
- Would a consumer still meet the old behavior anywhere: a parallel surface, a fixture, a document, a sibling repository?
- Does the description still match the branch, and does its Test plan list only what I ran?

Commit message for a review round:

```
Address review round 1: sort order, bound filter, cache wrapper

The recent-runs loader truncated to the limit before sorting, and the
batched query has no ORDER BY, so a job could show an arbitrary status
instead of its newest one. The limit now applies after the sort. The
status filter was interpolated into SQL after quoting only; it is now a
bound parameter, and the field name is checked against the loader
allowlist. RecentRunsCache is removed: it wrapped the library cache for
one caller, so the loader now uses the library cache directly.

Behavior change in tests: test_recent_runs_respects_limit builds its
rows out of order and asserts the newest status.

<the co-author trailer, exactly as the session gives it>
```

Corrected lines of the description, after that round:

```
## Changes
- `loaders/recent_runs.py`: sorts by `updated_at` before applying the limit, so each job shows its newest status.
- Removed `loaders/recent_runs_cache.py`: the loader uses the library cache directly.

## Test plan
- [ ] `poetry run pytest tests/unit` (Validation: count and baseline)
```

Ask the person, in one AskUserQuestion batch with your recommendation first, only about a finding that cannot be fixed here, or whose fix would change behavior, a contract, stored data or a decision the person made. When a fix has several reasonable shapes, choose one by the rule on how to decide. A security gap that cannot be closed here is always asked: shipping it is the person's risk. Each question says what a person or a caller would meet, in plain words and without file names, and each option what it commits the work to; the shared rule on how to ask has the rest.

### Questions to ask here

- Can this be simplified for now?
- Does the test fail without the fix?
- Did this fix add machinery?
- Is the description still true?
- What did I run, and what did it print?
