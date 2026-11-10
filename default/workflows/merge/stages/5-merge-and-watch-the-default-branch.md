Post the merge notes on the pull request before you merge: the checklist, the prerequisites, the decisions, and the commits added since approval. A reader must be able to see why this was safe to merge without asking you.

Merge when all of this is true at the current head:

- every item of the checklist is True, Fixed or Not applicable;
- every prerequisite is Verified or Done;
- the required checks are green, and tests that run only by hand were run by Validation, and again on this head when the update touched them;
- every review thread is resolved and every thread of the automated reviewer has a verdict;
- the pull request is not a draft and carries no hold;
- the branch contains the latest default branch.

Then:

1. Read the title one last time. It is the commit subject from here on.
2. Mark a draft ready only when the reason it was a draft is gone.
3. Merge with the repository's method: a squash unless the repository configures another. Never force, never bypass a protection, never use an administrator override, never enable automatic merging. A protection that blocks the merge is reported as it was given.
4. Confirm what landed: the subject is the title with the pull request number, the body lists the branch's commits, the co-author trailers are there. Record the hash.
5. Watch the default branch's checks, with one watching command, and every workflow the merge triggered. A deploy is proven when the running application reports the new build version. Never claim a check you did not watch pass. For a changed API the proof reads like this: "the affected v3 endpoints exercised via direct HTTP and through both the generated Python and TypeScript SDKs ... 36/36 each".
6. Merge the ticket's next pull request only when the default branch is green with this one and the release the next one pins exists. That release is the person's step: ask once, with the version you computed and the exact steps, then verify that it exists and contains this merge. When the tag predates this merge, the consumer bumps again and says why: "0.5.2 was tagged before the upstream pull request merged".

Landing a stack: merge the base part first and wait for green. The squash changed the hashes, so retarget the next part to the default branch, merge the default branch into it, and resolve each conflict in the first part's files to the default branch's version. Rerun its checks, correct its counts, then merge it. A core part that is red by itself never merges alone: merge the stacked part into the core branch first and land the two as one squash, or ask.

When the default branch breaks, decide at once between a revert and a fix forward, by the rule in "Migrations, rollout and rollback". The revert or the fix is this ticket's pull request, so the ticket's approval covers it; it still goes through the checks. Rerun a failed check once to rule out a flake, then read its trace; never rerun until green. Say which you chose and why, on the pull request and to the person.

A revert's pull request:

```
Title: Revert "Route archived files to a second storage account"

## Changes
- Reverts a1b2c3d. The staging deploy came up and failed its health check: every file route returned 500. The cause was not found within fifteen minutes.

## Test plan
- [x] `poetry run pytest tests/`: 131 passed, 1 skipped (the count before the change)
- [ ] Staging comes up after this merges and reports the previous build version

## What brings the change back
- The cause found and fixed on the reopened branch, with a test that fails on a1b2c3d.
- Nothing was written in the new shape, so the revert loses nothing.
```

Add to the merge notes when it is done:

```
### Landed
<hash> "<subject>" on <default branch>, <date>.
Default branch: <the checks and their result at that commit>.
Triggered: <each workflow the merge started and how it ended>; <environment> reports <version>.
```

Filled:

```
### Landed
a1b2c3d "Route archived files to a second storage account" on main, 2026-11-04.
Default branch: 4 of 4 checks green at a1b2c3d.
Triggered: the staging deploy finished green; staging reports 0.0.0-dev.412. Production is unchanged until a release is published.
```

When it broke:

```
### Landed, then reverted
a1b2c3d merged at 14:02. The staging deploy came up and failed its health check: every file route returned 500. Cause not found by 14:17.
Reverted by pull request 13 (b2c3d4e) at 14:21; the default branch is green and staging reports the previous build.
Lost by the revert: nothing; no file was archived in the new account. Brings it back: the cause found on the reopened branch, with a test that fails on a1b2c3d.
```

**Questions to ask here**

- Is everything green at this exact head?
- Is any thread still open?
- Does the title say what landed?
- Is the default branch green with this in it?
- Did staging come up? What version does it report?
- It broke. Is a revert faster than a fix? What would the revert lose?
