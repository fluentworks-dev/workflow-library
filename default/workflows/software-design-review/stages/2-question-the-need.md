Before judging whether the diff is correct, judge whether it is necessary. Judge each part of a design the same way, and prefer the simplest, most minimal, most straightforward form a reader could grasp at a glance. This pass is never skipped, even on a design that looks clean.

**Trace both ways.**

1. List every surface, component, process, store, table, column, route, setting, flag, concept, interface, file and task the designs add `[+]`, change `[~]` or remove `[-]`. Beside each, write the outcome in scope.md it serves. What serves no outcome is scope that crept in.
2. List every outcome and anti-goal in scope.md. Beside each outcome, write what delivers it and what proves it. Beside each anti-goal, write what could breach it. An outcome nothing delivers is a gap. A design that can breach an anti-goal is a blocker.

**Look for the smaller form of each part,** in this order:

1. Does it need to exist? Could the change be made by deleting code instead of adding it?
2. Does something already do it: the platform, the framework, a built-in feature of the technology (change data capture for change tracking, triggers for audit trails, framework middleware for identity), a helper or primitive in this repository or a sibling one?
3. Is this layer, wrapper, class or file redundant: a class with one method and one caller, a file with one constant, a wrapper around one field?
4. Is it built for a need that does not exist yet: a flag nobody sets, a variable added "just in case", a generic abstraction with one user, a mode where a queue would do? When to extract follows the rule on how to decide: nothing generic for one use; code repeated for one concept at its third copy.
5. Is it separable? A mechanism that can ship later without breaking the outcome leaves this ticket and is named as a seam.
6. Is it stored when it could be computed?

Keep operator-facing options on a machine: that is the one place where fewer options is not the simpler design.

**Enumerate what the smaller form removes,** by name, so the saving can be checked: for example, fixing a duplicate-job bug without new machinery removes `retry_ledger.py`, `_find_pending_jobs`, the branch that catches the data integrity error, and the reread after create. Then write what it gives up and whether it changes what a person can do. A form that gives up nothing in scope.md is a fix. A form that gives something up goes to the person only when the saving is large. Scope that narrows during review is the review working: a revision that applies a review can remove more lines than it adds.

**Judge the fix itself.** When a design answers a symptom with new machinery and the cause could be fixed with none, that is a blocker.

**Do not add.** A thing you expected and did not find is a finding only when an outcome fails without it: defaulting to adding the missing thing causes scope creep and over-engineering. A limit accepted with its number and the point to revisit it is a decision, not a gap: for example, the number of accounts is well below 1,000, results are cached for 10 minutes, and the limit is revisited if the count grows past that threshold.

**Questions to ask here**

- What is the point of the changes in this file? Are they necessary, or a workaround?
- Is this change relevant to the ticket? If not, it goes.
- Is this required, or optional? If optional, simplify it for now.
- Would it be simpler to use a different queue?
- What does this new mode add on top of what already exists?
- Can this feature wait until we know we need it?
- Is this still used? If not, remove it.
- Is this file too small to be its own file?
- Can the stored `id` go?
