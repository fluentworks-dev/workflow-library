Post each finding as an inline comment on the line it concerns, all in one pending review that you submit once. Never submit comment by comment, and never leave the review pending.

Give each finding one severity:

| Severity | Means | Outcome |
|---|---|---|
| blocker | Wrong, unsafe, breaks a consumer, or a claim in the description is false | Fixed before this workflow ends, or asked |
| simplification | The same result with less: a file, a layer, an option or a mechanism can go | Fixed here when the simpler form is clear |
| nit | Small and local: a name, an order, a comment | Fixed here; never invented |
| follow-up | Real, and not fixable in this change: another repository, a decision of the person, larger than the ticket | Written down with enough detail to file |

Template:

```
**[severity]** <the concern, in one or two sentences: what happens, with which input, and why it matters>
**Recommended:** <the concrete fix: a name, a type, a path, a code sketch, or a link to the reference implementation>
[Verified: <what you ran and what it showed>]
[That removes: <what the simpler form deletes, by name>]
```

How to write it:

- Three forms: as a co-owner, in the plural ("We should ...", "Let's ..."); as a question, when uncertain or when the question carries the proposal; as a hedged suggestion ("probably", "I think", "I would ...").
- The reason rides as a trailing clause: "so that", "since", "This way".
- The alternative is concrete: a name (`host_config_version`), a type, a path shape (`/app/workspace/{workflow_id}/...`), a link to the reference implementation, a code sketch. When you find a real issue, propose options, not just complaints.
- Not "This is over-engineered." but "Would it be simpler to do this simply by using a different queue? Separate worker modes do not seem needed; the same worker on a different queue already solves the problem." Not "Don't store this." but "The stored `id` can be derived from the key, so it can probably be dropped for now."
- A concern without a concrete fix is not posted. Every claim is verified first, and the comment says how.
- A simplification enumerates what it removes. A follow-up is only for what cannot be done here: a clear fix is never turned into one.
- A clean file gets no comment. Praise is specific or absent: name the built-in the author found, not a general compliment.
- One comment per problem: a problem repeated in five places gets one comment that lists the places.

### Before you submit, question your own findings

Reread the pending review as its author would, and drop or rewrite what fails:

- Did the ticket actually ask for this? Is the gap real against the request and the designs, or inferred? Only propose a change after the gap is confirmed real against the actual ask.
- Is the behavior I call wrong intentional? Read the history that added the line before calling a guard, a retry or an odd constant a mistake.
- Is there a simpler, cleaner approach than the fix I recommend? When my fix adds a file, an option or a layer, it is probably the wrong fix.
- Did I run every claim, including the ones that span files, or do I only believe them? An unverified claim becomes a question or goes.
- Would I hold the merge for each blocker? Is each nit a rule of this repository, or my taste?
- Does every comment carry a concrete fix, and does every simplification list what it removes?
- Could I do a follow-up here within the hour? Then it is not a follow-up.
- Did I call the clean files clean, or pad the review to look thorough?

One filled review, for a pull request that adds a loader of recent runs to a read-only query layer:

```
**[blocker]** `load_recent_runs` truncates to `limit` before sorting, and the batched query has no ORDER BY, so the status returned for a job is arbitrary instead of the newest one.
**Recommended:** sort by `updated_at` descending first, then apply the limit; add a test with rows arriving out of order.
Verified: built three rows out of order in a test; the oldest status came back.

**[blocker]** The `status` filter value is interpolated into the SQL string after quoting only, so a value containing a quote changes the query.
**Recommended:** bind it as a parameter, and check the field name against the allowlist the other loaders use.
Verified: `status = "x' OR '1'='1"` returned every row in a local run.

**[simplification]** I think we can cache without `RecentRunsCache`. It wraps the library cache for one caller and adds nothing to it.
**Recommended:** use the library cache directly in the loader, with the size and the lifetime as unit-named constants beside it.
That removes: `recent_runs_cache.py`, `RecentRunsCache` and its two tests.

**[nit]** `get_data` is a bit too general. Function names are verb phrases that say what happens: `load_recent_runs`?

**[follow-up]** The query still scans seven days of partitions per request. This change only stops the bleeding; the durable fix is repartitioning in the data lake's repository.
**Recommended:** file it there with the measured timings from this pull request.
```

The submitted review carries one line and no approval: this workflow reports, fixes and hands on. "Review round 1: 2 blockers, 1 simplification, 1 nit, 1 follow-up. Fixes follow on this branch."

### One thread, end to end

The register to copy. Picture an author who adds a `WORKER_MODE` setting so that a second deployment can run the heavy jobs. The review:

> Would it be simpler to send the heavy jobs to the second deployment simply by using a different queue? Separate worker modes do not seem needed; the same worker on a different queue already solves the problem.

The author defends the mode in a paragraph. The question is restated once, with a link to the line that already does the routing:

> The suggestion is not that the caller route anything. The workflow already pins the heavy jobs to the heavy queue (link to the line). The real question is: what does `WORKER_MODE` add on top of that? It looks like it could go, the worker configuration could stay the same everywhere, and the second deployment's task queue setting could point at the heavy queue. Is anything missed here?

The author removes the mode, agreeing that it makes the codebase simpler, and the approval comes the next day. A question that carries a concrete alternative; one restatement with evidence when misread; no change of tone; approval as soon as the mechanism is gone.

### When the pull request is someone else's

This workflow reviews the pipeline's own pull request. When you review another author's, these replace the labels and the fixing:

- Review file by file when it changes models, managers, workflows, services or CI configuration in a system you built or co-designed. Approve within hours, with at most one question, when it is a version bump, a schema addition, a release merge or outside your domain.
- Mark severity informally and rarely: "Nit:", "Optional:", or a note that something need not be done in this pull request. No blocking labels.
- Approve once the change is good enough, state what remains, and hand the merge to the author: say that most comments are addressed and that they can merge when they think it is ready. An approval does not wait for the answer to a question asked in the same review.
- Request changes only for design and mechanism (layering, the data model, concurrency and timeout semantics, the wrong infrastructure lever, configuration placement, a fix that added machinery) or one hard prerequisite (such as getting the latest version before merging). Never for style, naming alone or missing tests.
- Concede in one line when the author has more context: either way works for now, and it is their call. Restate once, with a link to the line, when misread. Decide for consistency when the question is a convention across repositories. When a fix is the wrong fix, do not approve it: say what the simpler fix removes and let the pull request close.
- Ask for proof as a run in the environment, not as unit tests: ask them to make sure it works in the development environment. Treat the automated reviewer's findings as part of the review, and point the author at the ones worth answering.
- Return for the second round and review the new code, including the choices the author made in answer to the first.
- When a pin is behind, name the exact version.
- With a newer engineer, state the general practice, not only the fix. Be warm and quick: thank them for a find.
- Thin review is the known weakness: an approval with no comment, on thousands of lines, in the minute before the author merges. So say what an approval covers ("Read the models, the migration and the workflow file. Did not read the components."); before approving a large pull request outside your domain read at least the description, the Test plan and every file that changes a contract or a trust boundary; answer within one working day; never put a must-fix item inside an approval; never paste assistant review text unattributed; never encourage a merge over an open consumer discussion.

### Questions to ask here

- Is this a preference rather than a rule?
- Did I run this, or do I only believe it?
- What is the fix, exactly?
- What does the simpler version delete?
- Is this a follow-up because it cannot be done here, or because it is inconvenient?
