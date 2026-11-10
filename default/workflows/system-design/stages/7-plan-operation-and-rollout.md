**Operation.** For each new component and each new failure mode write:

- what is logged: the event, with identifiers only, never secrets, tokens, personal data or raw rows; a recurring fault at most one line per interval;
- which request identifier ties a user's report to a log line;
- what is counted: for a request path, rate, errors and duration; for a queue, depth and the age of the oldest item; for a job, the time of the last success;
- what alerts whom: a failure of a whole run as well as of one task, sent to a group address;
- the first command or query someone runs to diagnose it.

Say plainly what the system cannot observe today, and name the first counter worth adding. Never design an alert on a metric that does not exist. A failure learned in operation gets its runbook the same day: exact commands, expected counts, the acceptance condition, and a table from symptoms to causes.

**Rollout.** Write the steps in the order they happen, so that the running system serves before, during and after each one: expand (additive schema, new configuration, new version), then the code, then the migration of data or consumers, then contract (removals) as a later change.

| Step | What | Who runs it | Safe to run twice | The check that proves it | Undoing it |
|---|---|---|---|---|---|
| 1 | Create the bucket and the worker role | The person, applying from the branch | Yes | A second plan reports "No changes" (first plan: 3 to add, 0 to change, 0 to destroy) | A destroy is the person's step; the bucket is empty until step 3 |
| 2 | Migration: create the table `exports` | The deploy, before the new version serves | Yes | The table exists on staging | Leave it; an unused table harms nothing |
| 3 | Deploy the service with the export routes and the worker task | The merge to the default branch | Yes | On staging, an export of the seed project downloads and its row is `ready` | Roll back the deploy; rows and files stay and expire in 7 days |
| 4 | Publish the SDK; the interface pins the release | The release, then the consumer's pull request | Yes | The consumer's checks pass on the released version, not on a development build | Pin the previous version |

Rules for the table:

- Operator preconditions are lines an operator can follow: "seed, then deploy"; "Apply order: the database before the application".
- A drop or a rename of anything the running system still reads is never in the same step as the code that stops reading it.
- Say when undoing is not the mirror of doing, and what is lost, for example that a rollback after cutover is silently lossy. A rollback of code leaves an additive schema in place, which is safe; a rollback after a data rewrite or a cutover is not.
- When several repositories are involved, give the merge order, the branch name they share, the development build each consumer pins meanwhile, and the swap to the release as a step of its own.
- When an apply must be timed against a cutover and not against the merge, say so.
- For applied infrastructure, the person applies from the branch and the merge follows a plan that reports "No changes". The expected plan counts are acceptance criteria.

**Settle it before it is applied.** Exploring in commits is cheap in code and expensive in anything applied. The usual drift: a new naming scheme for cloud resources every few weeks, each needing state moves and imports; a state bucket renamed more than once; archive semantics flipped back and forth inside one pull request. So:

- names, layouts and lifecycle semantics are settled in this document before the first apply, the first migration or the first published version;
- every resource name is pinned by an offline test before anything is created;
- a plan that wants to replace an applied resource for a naming cleanup keeps the old name with an override.

**Steps only a person can do** are listed again on their own, numbered, with the exact command and the condition that proves each one worked: applying infrastructure, setting a secret, approving a cost, publishing a release, running a destructive change.

**Release separately from deploy** when the blast radius matters: a flag or a version that can be turned on for one tenant first, the signal that widens it, and the change that removes the flag.

**Proof.** Say how the design will be proven: which behavior needs a test at which level (a contract test over the route table, a parity suite over adapters, a replay test, a test on the captured log), and what can only be checked by running the system, with the numbers to record.

**Ask the person**, in the session's batch, when a step is irreversible or loses data on rollback: say what is lost and give the alternative that keeps it.

## Questions to ask here

- what has to exist before this deploys? seed, then deploy?
- if we roll this back after the cutover, what is lost?
- who runs each step: the deploy, CI, or a person?
- is the consumer still pinned to a development build?
- how would anyone know this broke at night?
- did the plan come back with no changes after the apply?
