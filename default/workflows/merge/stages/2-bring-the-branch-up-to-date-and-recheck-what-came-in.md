Work in the ticket's worktree, never in the repository's primary checkout. When the branch is missing locally, fetch it. Read the working tree's status and last commit first: work an earlier session left uncommitted is committed or reported, never stashed and never overwritten from memory.

1. Fetch, and read what the default branch gained since the branch last had it: the commits and the files they touched.
2. When the branch is behind or conflicts, merge the default branch into it, resolve every conflict by keeping both intents, and push normally. Do not ask first. Where the repository requires a rebased branch, the rebase and the re-push are a step for the person: it goes in the batch of "Clear the prerequisites", with the exact commands.
3. Ask four questions about what came in: a file both sides changed; a caller or a dependency of the changed code; a change to which checks run; a migration, a setting or a dependency version. For each yes, rerun the related checks and the part of the validation's proof it touches.
4. Explain the diff. The files changed against the default branch are this ticket's files and nothing else. A diff you cannot explain line by line means the update or the base is wrong: stop and find out which.
5. Trigger the repository's on-demand check workflow on the new head when it has one. Wait for the checks with one watching command run by itself. Green on an earlier commit proves nothing.
6. Correct the test plan's counts to the new head, with the baseline beside them.

How to resolve:

| What conflicts | What to do |
|---|---|
| The same lines, both changes still wanted | Write the version that does both; rerun the tests of both |
| A file this ticket edits was moved or renamed | Apply this ticket's edit at the new location |
| A feature this ticket extends was removed | Drop the extension and say so in the merge notes; ask only when the ticket's outcome depended on it |
| A migration number the default branch took | Renumber this ticket's migration and every reference to it; never edit one the default branch carries |
| A lock file, a generated contract, a snapshot | Take the default branch's version, then regenerate with the repository's own command; never merge it by hand |
| A version file both sides bumped | Take the default branch's version and bump from there by the repository's rule |
| Keeping one side would undo what the other ticket decided | Ask the person: both intents, and what each option commits the work to |

Repeat this stage whenever the default branch moves before the merge. Three rounds that end without a green, current head is a blocker: stop and report what keeps moving.

When the update added commits after a review, announce them on the pull request before the merge:

```
New commits since approval:
- <hash> <subject>: <what it changes and why it was needed>
Checks at <head>: `<command>`: <count> (before the update: <count>).
Resolved by hand: `<file>`: kept <the intent from the default branch> and <the intent of this ticket>.
```

Filled:

```
New commits since approval:
- 9c8d7e6 Merge the default branch: brings in the renamed upload hook.
- 1f2e3d4 Renumber the migration to 0019: the default branch took 0018.
Checks at 1f2e3d4: `poetry run pytest tests/`: 136 passed, 1 skipped (before the update: 131 passed, 1 skipped; the 5 new tests came in with the hook).
Resolved by hand: `src/routers/v2/files.py`: kept the renamed hook from the default branch and this ticket's `association_name` argument.
```

Record under "Decided for you": each conflict you resolved and how, each renumbering, each regenerated file.

**Questions to ask here**

- What came in since the review? Does any of it touch what this changes?
- Did the default branch take our migration number?
- Is any change here not relevant to this ticket?
- Are the checks green at this head, or at the one before?
- Which side did we keep? Did we lose anything the other ticket decided?
- The test count moved. Why?
