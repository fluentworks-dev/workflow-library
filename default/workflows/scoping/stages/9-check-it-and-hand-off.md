A filled scope.md, abridged, for the register:

```
Status: scope, revision 1, 2026-11-04.

## Goal and value
Staff who export an order list larger than about 50,000 rows get "Export failed" every time,
and a retry fails the same way; today they split the list by hand. 41 exports failed in the
last 30 days, 38 of them for this reason (request log).
Value: staff export any list in one step. Output: an export of up to 1,000,000 rows finishes.
Measure: exports cut off at 60 seconds, per month: 38 today, 0 after.

## Options considered
[the options table] Recommendation: A, exports of any size finish. [objection and answer]
Looked for what exists: the product has no other export path; the platform offers a job
queue (recorded for System Design).

## In
- A person starts an export of any list up to 1,000,000 rows, and it finishes.
- The person can leave the page while it runs, and finds the file on return.
- Also fixed here: the export of an empty list no longer shows an error.

## Out
- A Retry control: the cause is removed, and the 3 other failures have no named cause yet.
- Export formats other than CSV: nobody asked.
- Not to commit: the sample lists used for testing.

## Anti-goals
- An export never hands over a partial file as if it were complete.
- A person never sees another person's export.
- No copy of the order list is kept after the file is produced.

## Outcome
[O1 to O4, as in "Write the outcomes"; O3. A finished file can be downloaded for at least 24 hours.]

## Extent
[as in "Map the extent"]

## Seams
- A Retry control: needs each failure to carry a cause the product can show. O4 keeps it open.

## Decisions
| Decision | Choice | Reason | Decided by |
|---|---|---|---|
| Cause or symptom | Fix the cause, no Retry control | 38 of 41 failures cannot succeed on a retry | The person |
| Exports running at a rollback | Lost; the person starts again | Rare, and no stored data is lost | The person |

## Decided for you
- A list with 0 rows exports the header row only.
- An export keeps running when the person signs out.

## Estimate
[as in "Size and slice the work"]

## Slices
This ticket only.

## Open questions
- Product Design: where a finished export is found; what is shown while it runs.
- System Design: where the state of a running export lives; retention of finished files (O3).
- Code Design: none.
```

Before you save, check the document:

1. Every item under In traces to the value. Every outcome can be observed by someone who did not write it.
2. No product question remains, in Open questions or under Settle before first use. Whether the product keeps or drops something people can see was asked, not assumed.
3. Nothing says how it looks or how it is built, except as an open question or a named constraint.
4. Every number has a unit and a reason. Every guess says it is one.
5. Every repository is one somebody named, or was flagged and answered.
6. What you ran to check the facts in the ticket is written, with counts. What you could not check is an assumption.
7. In and Out do not contradict. Decisions and Decided for you do not overlap. A design workflow marked "nothing to do" agrees with In: nothing under In is a surface, a stored change or a code change for that workflow.
8. It thinks from the point of view of whoever uses the data, and it says how the product behaves in different scenarios, not only when everything works.

Then check it against the ways scoping commonly drifts:

| Drift, and what it cost | The check |
|---|---|
| Names of live things changed after the fact: one naming scheme after another, a storage location renamed twice, an identifier dropped, restored and dropped again, each needing moves of live state | Every name that will be applied or published is settled here |
| The meaning of a state flipped back and forth inside one pull request | Every state the scope adds says what it does, what it keeps, and whether it can be undone |
| Fixes made before the evidence, then reverted | For a symptom ticket, the cause is named, with file and line |
| Large volumes of logs and samples in pull requests | The by-products that must not be committed are named under Out |
| Pull requests carrying hundreds of foreign commits | The base branch is named |
| A core change merged red while its tests sat in the next pull request | Every slice passes its checks alone |
| A serious finding neither fixed nor answered, then copied into a second repository | Every finding is in, or out with its owner, or a recorded known gap |
| A removed route that still had a caller | Every removal names the consumers that were searched |
| Documents describing what the code no longer does | The documents this change makes wrong are under In |
| Structure churn: shared pieces introduced, inlined, restored and deleted within one pull request | Scope fixes the outcome and leaves the shape open; the design workflows settle the shape in a document before any code |
| A contract merged over a consumer's open objection, then renegotiated once it had consumers | A disagreement with a consumer is settled with the person here, and recorded under Decisions |
| Mechanics kept by habit: a rule of one ticket per task and its status tooling outlived their use | The agreement keeps its current shape (goal, in and out, outcome, repositories), not the tooling of an earlier period |

Do not ask the person to approve scope.md. It records decisions they made and choices they can overturn at the hand-off.

When the person's answer changed what the ticket is, revise the title and the description of the ticket in place now, and keep the original request as one line. When you cannot edit the ticket, put the revised text at the top of your summary for the person to apply:

```
Title: Exports of any size finish
Description: Staff export any order list up to 1,000,000 rows in one step. See scope.md.
Originally asked: a Retry button on failed exports. Changed on 2026-11-04 by the person's
decision: 38 of 41 failures cannot succeed on a retry.
```

Your summary is one line that says what will be built and why, then the link:

```
Scoped the export ticket to fix the cause instead of adding a Retry button: exports of up to 1,000,000
rows finish, because 38 of 41 failures could never succeed on a retry. scope.md: [link]
```

Before you finish, report your status with the progress tool: done, or blocked with the reason.

**Questions to ask here**

- Read it cold. Do you know what gets built and why, in one line?
- What is each change for? Is there anything in here nobody asked for?
- Which of these did the person decide, and which did you? Is that visible?
- What did you actually run? Where are the counts?
- Is anything left "for later" with no owner?
