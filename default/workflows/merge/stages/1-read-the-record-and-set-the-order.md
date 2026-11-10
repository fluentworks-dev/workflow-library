Find every pull request the ticket names, in every repository, and read each one's state on the host: open, draft, merged or closed; its base and head; its checks; its review threads.

**Nothing to do.** When every pull request is already merged, say so and do not redo the merge; go to "Finish the follow-ups". When they are merged and nothing is left (no consumer to move, no step for a person, nothing of this ticket to clean up), write the merge notes in three lines and finish:

```
## Merge notes
Already merged: pull request 12 at a1b2c3d on 2026-11-03; the default branch is green at that commit.
Nothing is left: no consumer to move, no step for a person, no branch or worktree of this ticket remains.
Checked: the pull request's state, the default branch's checks, `git worktree list`, the ticket's open follow-ups.
```

**Read, in this order**, and take what each settles as settled:

1. The ticket, its comments, and the answers to earlier questions.
2. scope.md for the outcome that proves the work done; system-design.md for rollout, contracts and infrastructure; code-design.md; design-review.md. Read the ones that exist: a board made from an older template has no System Design or Design Review column, so system-design.md or design-review.md may be missing; say in the merge notes which was missing.
3. The pull request record: the description, every review thread, the code review summary, the validation summary. List what each left open: unchecked boxes, deferred findings, "Known follow-ups", a standing "Do not merge yet".
4. The repository: its guidance file, the README's operations and status sections, the pull request template, the workflows a pull request triggers, the workflows a push to the default branch triggers, and the version rule.

**Note each repository's conventions**, as "The repository's convention wins" lists them, and what a merge will trigger there.

What repositories commonly look like, by shape; read it as what to expect, never in place of reading the repository:

| Repository shape | Working documents | What CI gates | Target, versions, pins |
|---|---|---|---|
| REST service with generated clients | removed; one architecture document kept | pre-commit and both test suites | squash; three lanes; publishes SDKs that consumers pin exactly |
| Backend for a frontend | removed | pre-commit, tests with coverage | the same lanes; pins the upstream SDK, development build then release |
| Web application | removed | dedupe, lint, typecheck, browser tests on three browsers | pins the backend SDK exactly |
| Data lake pipelines | removed; the platform design kept | bundle validation only; tests by hand | a tag after every merge deploys production |
| Read-only GraphQL layer | kept on the default branch | lint only | staging on push; a backup slot on release, swapped by hand |
| Robot control stack | banned; gone before the pull request opens | a build only; reviewers demand a run on the robot | `develop` and `release` flows; a `VERSION` file CI checks |
| Infrastructure applied by hand | removed | nothing; a person plans and applies | every apply by hand; the provider pinned in one root file |
| Infrastructure modules with a consumer repository | removed | format checks, offline tests, a plan | apply on merge except bootstrap; the consumer pins the modules by SHA |
| Application built fast with an assistant | kept, linked from the README | typecheck only | prefixed subjects; deployed through a platform |

**Confirm identity and base.** Each pull request is the one the ticket names and targets the right branch. A diff that carries commits from other work is mis-based. Retarget it when the branch was cut from the right place. Otherwise ask the person, with the number of foreign commits, whether to close it and reopen it on the right base. Never repair a base by rewriting the branch.

**Find the holds.** A draft, a "[BLOCKED ...]" title or a "Do not merge yet" alert has a reason somewhere in the record. Find it. A reason that is now resolved is cleared in the checklist stage; one that is not is a prerequisite.

**Set the order.** Across repositories the provider merges first, then each consumer. A stack merges base first. Check that every body's `Related PRs` block states the same order, and correct the ones that do not.

Record under "Decided for you": the order you set, a base you retargeted, a convention you had to infer.

**Questions to ask here**

- Is this the pull request the ticket names? Is it on the right base?
- What merges first? Is that order written in every pull request?
- What does merging this trigger? A staging deploy? An apply?
- What did the review and the validation leave open?
- Why is this still a draft?
- Does this repository have a description template? Did we follow it?
- Who else depends on what this changes?
