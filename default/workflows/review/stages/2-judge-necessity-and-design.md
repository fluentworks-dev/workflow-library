Look at the change whole before any line. Necessity is the first lens: before judging whether the diff is correct, judge whether it is necessary, and bias toward the simplest, most minimal, most straightforward form a reader could grasp at a glance.

Ask of the change, and write each answer down:

1. Does each new file, type, layer, option and dependency need to exist? Does this file need to exist? Is this layer redundant?
2. Could the change be made by deleting code instead of adding it? By using what this repository or a sibling repository already has?
3. Is anything built for a need that does not exist yet: an option nobody sets, an abstraction with one user, a parameter no caller passes? For example, a worker count option can wait until someone needs it.
4. Does each piece sit in the layer that owns its concern? Does a layer know its children's concerns? Are invariants enforced where every caller passes?
5. Can an invalid state be written down? Is one fact stored in two places, or stored when the structure already determines it?
6. Do two things share a type because they look alike today, though they mean different things?
7. Do the existing consumers of everything changed keep working? Search for callers of every removed or renamed route, parameter, field, setting and default, in this repository and in its siblings.
8. Is a separable mechanism riding along that could ship as its own change? For example, a new file-reference validation that spans six definitions across four files leaves this pull request entirely and ships as its own follow-up, with the list of what to remove.

When a simpler mechanism exists, ask for it and enumerate what it removes, by name: for example, the fix needs no new machinery, and that removes `version_chain.py`, `_find_open_versions` and the re-read after create.

Now read the design documents the ticket has, in order: scope.md, product-design.md, system-design.md, code-design.md and design-review.md. A board made from an older template has no System Design or Design Review column, so system-design.md or design-review.md may be missing; compare against the ones that exist and say in the findings which were missing. Compare:

- What the code does that the designs did not ask for. That is scope that crept in, unless the description records it under Decided for you.
- What the designs asked for that the code does not do. That is a gap.
- Where the code departs from a design without a recorded reason.
- Where the default branch has moved since the designs were written. Follow the default branch and note the difference.

Question the need before you call something missing: did the ticket actually ask for this? Only propose a change after the gap is confirmed real against the actual ask. Defaulting to adding the missing thing causes scope creep and over-engineering.

What to decide and what to ask:

- When the better structure is clear, it is a finding (a simplification, or a blocker when the current structure is wrong), and you restructure it in the fix stage, even when that moves code between files.
- Ask the person, later and all together, only when the structure cannot be fixed here, or when the change would alter behavior, a contract, stored data or a decision the person made. Several reasonable shapes with no clear winner are yours to choose between.
- A design decision that is recorded and sound is settled, even when you would have chosen differently. Do not reopen it on preference.
- The one standing exception to the smallest mechanism: an option an operator may need when things go wrong stays. More flexibility is good when it gives more options when things go wrong.

### Questions to ask here

- Would it be simpler to do this by using a different queue?
- Is this required? It seems optional; can it be simplified for now?
- Is this a workaround rather than a fix?
- Can the `id` be dropped?
- Who else calls this? Did anyone search the consumers?
