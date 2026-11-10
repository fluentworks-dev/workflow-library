**Ask now, once.** Every point earlier stages left for the person goes in one AskUserQuestion batch before you write, the one that shapes the rest first, each put as the rule on when to ask says. Apply the answers, then write. When nothing is the person's, ask nothing.

Save system-design.md on the ticket with these sections, in this order. Keep every section and write "None" where there is nothing. Use file paths, class names and snippets: the reader knows the repository.

```
Status: draft, revision <N> (<what it replaces, or "first revision">)

## Intent
One paragraph: the problem as someone experiences it, then the key insight.

## Goals
Three to six outcomes, each checkable.

## Non-goals
What this change will not do.

## Anti-goals
What the design must not do even by accident; each a sentence a reviewer can check.

## Constraints
Rules the builder must obey. Facts the builder can rely on, each with where it was verified.
Anything unverified as "Assumption:" with what it depends on.

## What it looks like
Numbered concrete examples, before any abstraction.

## The model
### Today and after
Text diagram of context and containers; existing parts and new parts marked.
### Components and processes
### Stored data
Schemas as code; source and derived; each change classified additive or breaking.
### Contracts
Per contract: examples, behavior per version, errors with the consumer's next action, consumers.
### Infrastructure and configuration
Resources with size, price and reason; values that select an environment; values that tune; secrets.
### Per repository
Only when more than one repository changes: upstream first, then each consumer, with what changes in each.

## Failure and concurrency
The table of writes; delivery semantics; bounds; timeouts and retries; where failure stops.

## Compatibility
The consumers of what changes; what each sees before, during and after; deprecations.

## Security
The threat table; the standing positions; accepted gaps.

## Capacity and cost
Numbers with unit and reason; capacity lines; cost paths, minimum, estimate.

## Operation
What is logged, counted and alerted; how a failure is diagnosed; what cannot be observed yet.

## Decisions
| Decision | Choice | Reason | Decided by |
Under the table, the long form for any decision that is contested, costly or hard to reverse:
context, options table, recommendation, why the others lost, how to revisit it.

## Decided for you
One line for each choice made without asking, so the person can overturn it.

## Seams
What is deliberately not built, named so nobody builds around it, each with the contract that must stay true.

## Done means
Observable outcomes.

## Left to the builder
What is intentionally unspecified.

## Testing and rollout
Which test pins which edge case; the rollout table; the steps only a person can do.

## Open questions for Code Design
What the code design must settle. No product question may remain here.
```

Rules for writing it:

- **Intent** is the problem as someone lives it, in this register: "Nobody can answer 'which job writes this column' or 'which systems read it' without reading pipeline code."
- **An anti-goal** is a trap inside the scope, written so that a reviewer can check the design against it: "A second place to define a column."; "A stored total that could disagree with the rows it sums". If you cannot think of one, ask what the next contributor would add by accident.
- **A seam** is written as a contract: what a later change may assume, and what nothing may assume.
- **Done means** is observable: a named test passes, a named invalid input fails with a message naming the cause. Every goal has a line there.
- **Left to the builder** is what you chose not to specify: "`argparse` is enough".
- **Each row of Decisions is an architecture decision record in one line.** In "Decided by" write the person's name for a decision they made, and "System Design" for one you made.
- **Order the model by controversy**: the most fundamental and contestable part first.
- **Every number has a unit and a reason. Every fact says where it was verified.**
- **When a system-design.md already exists on the ticket, write the next revision**: keep what still holds, edit the copy that reading it saved and save it from that path (write it from scratch only when its concepts change), and say in the status line what it replaces. Each revision carries one conceptual change, stated in a sentence, and the direction is toward fewer concepts: a good revision drops a concept the design turned out not to need, or narrows the design to what the first consumer needs. A revision that adds a concept owes a reason.
- **Expect a check, not a critique.** A fresh-context reviewer looks for non-obvious tradeoffs, departures from existing patterns, hidden scope expansions, load-bearing assumptions, and drift from the stated goal. Flag those five kinds yourself, in the document.
- **Name what must outlive the ticket** and where the builder writes it: a comment at the point of temptation, a line in the repository's guidance file, a runbook entry, a section of the pull request.
- Write no tasks, no file map and no code structure. Those belong to Code Design.

## Questions to ask here

- can someone build this from the examples alone?
- which of these goals can a test check?
- what must this never do, even by accident?
- what are we deliberately not building, and is it named?
- what is this change for? if it is not relevant, remove it.
