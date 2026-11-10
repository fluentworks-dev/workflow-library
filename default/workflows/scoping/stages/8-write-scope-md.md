**Ask now, once.** Every point earlier stages left for the person goes in one AskUserQuestion batch before you write, the question that shapes the rest first, each put as the rule on when to ask says. Apply the answers, then write. When nothing is the person's, ask nothing.

Save the ticket document scope.md with these sections, in this order. Keep a section with "None" instead of dropping it.

| Section | Holds |
|---|---|
| Status | One line: the revision, the date, what it replaces |
| Goal and value | The problem as someone experiences it, in one paragraph. Then value, output and measure |
| Options considered | The options table, the recommendation, the strongest objection and its answer, where you looked for what exists |
| In | What a person can do or see happen: the complete set, and the "also fixed here" items |
| Out | What is left out, each with its reason; by-products that must not be committed |
| Anti-goals | What the work must never do, one checkable sentence each |
| Outcome | O1, O2, ...: observable and testable, with numbers, units and reasons. Never renumbered |
| Extent | The section from "Map the extent" |
| Seams | What is deferred, and what must stay true so it can be added |
| Decisions | A table: Decision, Choice, Reason, Decided by. What the person decided, attributed |
| Decided for you | One line each: what you chose without asking |
| Estimate | Agent time, person time, uncertainty, proof |
| Slices | The slice this ticket is, and the others in order |
| Open questions | Grouped for Product Design, System Design and Code Design. No product question may remain |

Rules for the text:

- Goal, In, Out and Outcome name things by what they do for people, never by class or file. Extent and Open questions give repositories, paths and contract names exactly.
- No mechanism appears outside Open questions and named constraints. If you wrote how it looks or how it is built, move it to Open questions.
- Every unverified statement begins "Assumption:" and says what it depends on.
- A revision edits the copy that reading scope.md saved and is saved from that path, with the Status line saying what it replaces; it is written from scratch only when its concepts change. Never append a list of changes.
- Decision records travel with the change. Under Options considered, add a "Considered but not changed" line for each idea that was weighed and declined, so that it is not raised again. Under Extent, spell out any one-time loss on upgrade in plain words.
- Before saving, reread for two sections that contradict each other and for any sentence with two readings. Pick one reading and write it, or ask when it is a product choice.

**When there is nothing to scope**, write the three-line form and finish:

```
Status: scope, revision 1, 2026-11-04. Nothing to decide.
The ticket is fully specified: [the change in one line], in [repository], proven by [observation].
Design workflows: Product Design nothing to do; System Design nothing to do; Code Design yes.
```

**When the decision is not to build**, write the three-line form and finish. Filled in:

```
Status: scope, revision 1, 2026-11-04. Not building. Decided by the person.
Why: a second export format was asked for by nobody who exports; 0 requests in the tracker.
Instead: Nothing. Reopen when a named consumer needs a format other than CSV.
```

**Questions to ask here**

- What is the point of this section? Without it, would a reader still know what to build?
- Is any product question still open? Then it is not done.
- Could a reviewer check this sentence, or is it a wish?
- Does In contradict Out anywhere?
- Did you decide how it looks or how it is built? Move it.
- Would someone without this conversation understand it?
