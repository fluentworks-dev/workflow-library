For each part below, write what the ticket already settles and what is open. A part is settled only when the ticket states it explicitly and with one reading, or when only one option is viable. Then close what is open: decide it, find it out, or ask.

- **Goal**: the problem as someone experiences it, then the value statement from the previous stage.
- **In**: the complete set that must change together for the outcome to hold everywhere a person meets it, written as what a person can do or see happen.
- **Out**: what is deliberately left out, each with its reason, including by-products that must not be committed. When the ticket lists many needs, sort them into must, should and could; each should and could is out with its reason, or a seam.
- **Anti-goals**: what this work must never do, even by accident. One checkable sentence each.
- **Seams**: what is deferred, and what must stay true so it can be added.

Find the anti-goals by asking what the easy implementation would do that the product must not: show something to a person who should not see it, lose what a person typed, keep a second copy of a fact, guess a value the person left blank, break a consumer, spend money without a confirmation.

Then test each item under In:

1. Remove it. Is the value still delivered? Then it moves to Out, or leaves.
2. Is a product rule behind it undecided? Ship what shows information, gate what changes it, and write the sentence that says so ("item tags are deliberately not editable for now").
3. Does it store or ask for something the product can already work out? Scope the computed figure.
4. Does it repeat a rule another system owns? Scope "rely on it".
5. Is it a mechanism of its own? Cut it to its own ticket and list what leaves with it.
6. Would a person still meet the old behavior anywhere? Then In is incomplete.
7. Is it a control an operator reaches for at a machine when things go wrong? Then removing it is not a simplification: keep it.

Write each deferral with the number or the event that ends it, for example:

- The account holds well under 999 items and results are cached for 10 minutes; revisit if it grows past that threshold.
- A hard limit of 1,000 items per project is a safe practical ceiling for this system.
- No reusable abstraction for top-N-per-key across loaders; if a second use case appears, extract the pattern then.
- Only one kind of report is loaded for now; generalize once more loaders are needed.

**When the scope is revised, count its concepts.** Each revision rewrites the whole document, says in its status line what it replaces, and removes a concept where it can: a dataset, an ownership overlay, a validation library. What is specified but deferred grows while what is built shrinks; a revision that applies a review and narrows the scope to what the first consumer needs comes out shorter. So a review of the scope is a reason to narrow, never to add. Each thing that leaves In becomes a seam with its contract, or goes to Out with its reason. When a revision adds a concept, say what it replaces.

**Decide and record under "Decided for you"**, one line each: edge cases, what is remembered between visits, limits and caps with their number, keeping a behavior that exists today, whether the other surface of a shared component gets the same change, which kinds of item count.

```
Decided for you
- An export that is still running when the person signs out keeps running; they find it on return.
- A list with 0 rows exports a file with the header row only (today's behavior, kept).
- Ceiling: 1,000,000 rows per export. Chosen by feel: the largest list today has 212,000 rows.
```

**Ask the person**, in the session's one batch, only about what the ticket covers and what a person can do or see at the end: whether the product keeps or drops something people can see, a second reading of the request, money, something hard to undo, an anti-goal or a deferral that rules out something they may want. The batch is asked before scope.md is written, the questions that shape the scope first; derive the rest from the answers, and ask a second batch only when an answer opens a new question that is the person's. "Assumed, say if you want otherwise" is not allowed.

**Do not ask and do not decide** how anything looks, reads or is built. Record each such point under Open questions, addressed to the workflow that owns it:

```
Open questions
- Product Design: where the person finds a finished export, and what they see while it runs.
- System Design: where the state of a running export is kept; how long finished files are
  kept (scope requires at least 24 hours, O3).
- Code Design: none.
```

After an answer: when it narrows the ticket, do not propose widening it to neighbouring things. When it asks you to explain, explain in your next question. When it stays vague, offer to proceed with what you have, or to stop. When an answer would need data the product does not record, write the unknown into scope.md and scope what would record it.

**Questions to ask here**

- What is this change for? If it is not relevant, remove it.
- Can a field such as an `id` be dropped? Does the product already know this?
- Does this need to be stored and committed now?
- Read only for now? Who decides the write side?
- What must this never do, even by accident?
- Is this a preference? Whose is it to decide?
- If this line is deleted, is anything lost?
