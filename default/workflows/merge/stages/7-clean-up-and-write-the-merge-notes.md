Remove only what this ticket created: its worktrees, its merged branches, its scratch files.

1. List the worktrees and find this ticket's.
2. For each, check for uncommitted changes and for commits that were never pushed. When there are any, those files exist nowhere else: list them and ask the person whether to commit, move or delete them. Never force a removal, and never stash.
3. Remove the worktree, then prune.
4. Delete a merged branch by the repository's own cleanup. Keep the branch of a pull request that was closed without merging, the original of a split, and any branch something live still fetches.
5. Stop only the processes you started, by the identifier you recorded when you started them, never by name or by port.
6. Remove the ticket's scratch files. Keep the evidence that proves the work: screenshots, logs, transcripts.

Complete the merge notes on each pull request. The whole comment, in order:

```
## Merge notes

### Pre-merge checklist (head <hash>)
<the table>

### Prerequisites
<the table, or "None" and why>

### Decisions
| Decision | Choice | Reason | Decided by |
|---|---|---|---|
| <a question this workflow asked> | <the answer> | <why> | the person |

### Decided for you
- <choice>: <reason, one line>

### Commits since approval
- <hash> <subject>

### Landed
<the hash, the default branch's state, what the merge triggered>

### Follow-ups
- Done: <what, and where>
- Open: <what, where, why not now, with whom, the trigger>

### Cleaned up
<worktrees and branches removed; what was kept and why>

### Project memory
<the facts this ticket added or replaced, one line each; or "Nothing new">
```

Keep every section; write "None" where one is empty. The parts after the two tables, filled:

```
### Decisions
| Decision | Choice | Reason | Decided by |
|---|---|---|---|
| Set the production variable before the merge, or only before the release | Before the merge | a release cut later would fail at startup with nobody watching | the person |

### Decided for you
- Merged the default branch into the branch instead of rebasing: the repository squash-merges, so the default branch ends the same and no force-push is needed.
- Renumbered the migration from 0018 to 0019: the default branch took 0018.
- Patch bump, not minor: the repository's rule keeps minor for a new table or pipeline.

### Commits since approval
- 7d6c5b4 Correct the README: uploads go to two storage accounts
- 3f2a9c1 Pin service-sdk to the 0.5.3 release

### Follow-ups
- Done: the changelog entry for 0.6.0; the README status paragraph now says the second account is live in staging.
- Open: delete the old copies of archived files from the first account. Not now: it is destructive and the person decides when. Due after one release in production. With the person.

### Cleaned up
Worktree and branch `archive-storage-account` removed in both repositories. Kept: the validation screenshots and the deploy log.
```

Before you finish, reread the notes against what you watched: every count in them is one you saw, and nothing claims a check you did not see pass.

Your summary says what landed, with the commit, and what is still open and with whom.

**Questions to ask here**

- Is there anything in this worktree that exists nowhere else?
- Did I start that process, or did someone else?
- What landed, and at which commit?
- What is still open, and with whom?
- Would someone reading only the pull request know why this was safe to merge?
