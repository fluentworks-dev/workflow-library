Do what the merge started, in this order. The usual sequence is: squash-merge; tag a release where releases deploy; publish the SDK; bump the consumers in their own pull requests in the stated merge order; refresh the README status paragraph when reality changed. The tag and the publishing are the person's; the rest is yours.

1. **Consumers.** Move each consumer in the stated order: swap its pin to the release as its last commit, rerun its checks, update its `Related PRs`, and take it through the checklist and the merge like the first.
2. **What the guidance file says to do after a merge.** Do it in full as written, without asking, in this session, right after the merge commit: for example, the design canvas and the design system page for every screen, page and copy the pull request changed, and anything else the guidance names. A tool that refuses a path is answered within its rules (build the files inside the ticket's working folder and publish from there), not with a question. Only what no tool of this session can do becomes a person step under Follow-ups, with the exact steps.
3. **What describes reality.** Refresh the README's status paragraph when something went live or a known gap opened or closed. Add the changelog entry or the release notes where the repository keeps them. Update a runbook whose commands changed. The status paragraph says what is live and what is known to be broken, for example: "Known app-side gaps, outside this repo".
4. **What was learned.** A failure met for the first time during this merge gets its recovery written into the README the same day, in the runbook form below. A convention that bit this ticket goes into the guidance file in one line, with the error it prevents. Keep the line short: "Markdown files other than `README.md` are read by models, not people: keep sentences as short as possible without losing information".
5. **What was left open.** Anything still open from the review, the validation or the body is finished now, in a follow-up pull request when the branch is gone. This is the last workflow: nothing after it picks up what you leave. What truly cannot be done here stays open as one line with enough detail to file as a ticket: what, where, why not now, and the trigger that makes it due (for example, left as is for now and addressed before access control is turned on). A deferral names its ticket: "Deferred to PROJ-142"; "Filed as issue 57 (deferred follow-up)".
6. **Other pull requests of this ticket.** Close each one that will not merge with one sentence naming the successor or the reason, and keep its branch: "Closing: branch was built on top of unreleased inventory-database commits. Reimplementing against origin/main."; "Closing: PROJ-142 canceled. Work will not proceed." "Superseded by pull request 14, which contains every commit from this PR and retargets the account".

A runbook entry:

```
## <Operation>
When to use: <the situation, one line>
Prerequisites: <role or credential, tool versions, what must already exist>
Steps:
1. `<exact command, with its full path from the repository root>`
   Expect: <plan counts or the output line>
Done when: <the acceptance condition, observable>
| Symptom | Cause | Fix |
|---|---|---|
| The plan shows the role as "to add" after a successful apply | the state was written under another key | compare the state key with the bucket listing; do not apply again |
Recovery: <commands for the failure that was seen, written the day it was seen>
```

Filled:

```
## First apply of the deploy role in a new account
When to use: a new account has no role for CI to assume yet.
Prerequisites: signed in as the bootstrap service principal, never as a person.
Steps:
1. `cd infra/bootstrap/deploy-role && terragrunt plan`
   Expect: 4 to add, 0 to change, 0 to destroy.
2. `terragrunt apply`
Done when: the plan job on the pull request reports "No changes" for the bootstrap units.
Recovery: a credential created and never bound is invisible to its owner. List it with the administrator role, delete it, and rerun step 2.
```

A changelog entry, where the repository keeps one:

```
## 1.4.0 (2026-11-04)
### Added
- Attach accepts an optional `association_name`, so one order can carry several files of one kind.
### Deprecated
- `GET /v1/files/{id}/content`: use `GET /v2/files/{id}/download`. Removed once the two remaining callers have moved.
```

A line for the guidance file, weak and durable. One such line reads "setup.py install_requires alone does not install tenacity during backend bringup".

| Weak | Durable |
|---|---|
| "Remember to update all the places when adding variables." | "A new required variable touches four places (template placeholder, script allowlist, workflow `env:`, the deployment environment) and the documentation table. Missing any one fails the deploy with 'Missing required environment variables'." |
| "Be careful with the pre-authorization." | "Do not remove the command-line pre-authorization on the application registration. Removing it breaks every local setup with `AADSTS650057`." |

Then collect everything that only a person can do into one ticket comment titled "After merge: a person must". One numbered line per step, in the order they must happen, each with the exact command or clicks and the condition that proves it worked. A weak step reads "Deploy to production and check that it works"; an exact one names the release, the place and the proof. Say what a rollback would lose when it is not the mirror of the merge, and which merged tickets the next release must carry. These are steps, not questions: do not ask the person whether to do them. When there is none, do not post the comment; say "Nothing is left for a person" in the merge notes.

```
## After merge: a person must

Merged: <repository and pull request> at <hash>; the default branch is green.

1. <Action>. Where: <place>. Run: `<command>` (or the exact clicks). Worked when: <observable condition>.

Order: <which step waits for which>
Rollback: <symmetric, or what it loses>
The next release must carry: <tickets merged and not yet released>
```

Filled:

```
## After merge: a person must

Merged: the service's pull request 12 at a1b2c3d and the backend's pull request 8 at d4e5f6a; both default branches are green, and staging reports 0.0.0-dev.412.

1. Publish the service release. Where: the service repository's Releases page. Draft a new release with tag `v0.6.0` on main at a1b2c3d or later, paste the notes below, publish. Worked when: the production deploy workflow is green and `GET /health` on the production slot reports `0.6.0`.
2. Apply the new index on production. Where: the service repository. Run: `python scripts/apply_indexes.py --environment production --dry-run`, read the plan, then run it without `--dry-run`. Worked when: a second dry run prints `0 to create`.
3. Swap the production slot. Where: the cloud portal, the service's deployment slots. Worked when: `GET /health` on the production address reports `0.6.0`.
4. Upload one file through the web application on production and archive it. Worked when: the file is listed in the second storage account and still opens from the order page.

Order: 1 before 2 before 3; 4 last.
Rollback: not symmetric after step 4. Files archived after the swap live only in the second account; swapping the slot back leaves them unreachable until it is swapped forward again.
The next release must carry: this ticket and the run-list fix, both merged and unreleased.

Release notes for step 1:
- Route archived files to a second storage account
- List runs when no store is given
```

In practice: applies when the repository uses Spark notebooks and Delta tables. The post-merge section for a backfill, whose proof is a count against the source ("52,000 rows, identical to the source warehouse with 0 missing, 0 extra, and 0 value mismatches"):

```
## Post-merge steps (reviewer to run)
1. Deploy the bundle (the `v1.14.0` tag does this).
2. Run once, in the production workspace:
   MERGE INTO conformed.operation_runs AS target
   USING staging.operation_runs_backfill AS source
   ON target.run_id = source.run_id AND target.run_date = source.run_date
   WHEN MATCHED THEN UPDATE SET target.is_complete = source.is_complete;
   Idempotent; never inserts or changes membership. The partition column is in the condition.
3. Worked when: `SELECT count(*) FROM conformed.operation_runs WHERE is_complete IS NULL` returns 0.
Rollback: the new column stays; readers on the previous version ignore it.
```

**Questions to ask here**

- What is left that only a person can do? Is each one a command with a condition?
- Is there a follow-up on this list I could have just done?
- Did reality change? Does the README status say so?
- Which merged tickets does the next release carry?
- Has it been run in the development environment to show it works? Which environment proves it, and who looks?
- Is anything still pinned to this branch?
