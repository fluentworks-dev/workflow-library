**Verify each finding before you record it.** Run the query, read the installed version, open the file, measure, and say what you checked: for example, verified locally with a 5-row table. A claim that spans documents is checked now, not noted. A finding that fails its check is dropped. When two designs differ on a fact that can be measured, do not settle it by argument: measure it in a scratch directory against test data when that is small, or have the design prototype the candidates and pick by an operational criterion; for example, prototype candidate storage formats on branches and keep the one that can be recovered with `zcat`. Never use the person's accounts or production.

**Label each finding.**

- **Blocker**: built as written it would be wrong, break a consumer, lose data or leave a security finding open. Block only on design and mechanism (layering, a shared data model, concurrency and timeout semantics, response typing, a wrong infrastructure lever, configuration placement, a fix that adds machinery, a hard prerequisite), never on style, naming alone or taste.
- **Simplification**: the same outcome with less.
- **Nit**: wording, naming or order that changes nothing that is built. Fix it, list it in one line, use it sparingly.
- **Follow-up**: real, and outside this ticket or owned by another repository. One line, never built here.

Be willing to call something clean with no concerns. No findings is a legitimate result; never manufacture a nit.

**Write each finding** as the documented answer to the blunt question you asked yourself. The question may be whether a size is the smallest that will do for a sandbox; what you write down is the checked answer with its cost, for example: trade-off to know, a single availability zone means an outage in that zone takes the sandbox down. Give where it is, what the design does, the alternative, the reason as a trailing clause ("so that", "since"), and what the alternative removes or costs. Write as a co-owner ("We should", "Let's"), ask when you are not certain, hedge honestly ("probably", "for now"), and always give a concrete alternative: a name, a type, a path shape, a link to the place that already does it. When you find a real issue, propose options, not just complaints.

| Weak | Strong |
|---|---|
| "The data model seems overcomplicated." | "Can we make this a map keyed by the association name? Clients look a link up by name, so a map removes the scan and the stored `id`." |
| "Security should be reviewed." | "The list route has no caller check. We should enforce it in the manager with the same policy as the point read, so that it holds for every route." |

**Sort each finding by whose it is.**

| The finding | What you do |
|---|---|
| The fix is clear and changes nothing the person decided | Fix it in the document and record it |
| It overturns a choice an earlier session made for the person, and the better option is clear | Change it and record it under "Decided for you" with the reason |
| It changes what the product does or leaves out, costs money, cannot be undone, reaches outside this workspace, or overturns the person's own decision | Ask |
| Two sound designs, no clear winner | Choose the one that is easier to change later, and record it under "Decided for you" with what it gives up |
| A defensible choice the person may not expect, where the build can proceed either way | Decide it; put it at the head of "Decided for you" with what it gives up; do not ask |
| The approach itself is wrong, and fixing it means designing again | Do not redesign; the verdict is "Not ready to build" |

The decision's owner decides, and a better option is a proposal, not a mandate.

**Fix in the documents themselves.** Do not leave a clear fix as a suggestion.

- Edit the local copy that reading the document saved, and save it from its path, once per document, after the person's answers are in; the stored document stays whole, so nobody reads a revision out of a list of notes. Its status line says what superseded what: `Status: draft, revision 2 (revision 1 approved <date>, superseded by this one)`. Retype a document only when its concepts changed.
- State the conceptual change in one sentence. A good revision needs fewer concepts than the one before; one that adds a concept names the outcome that failed without it.
- Carry the fix through every document it touches. List what a fix removes, by name, and name what it defers as a seam.
- Do not write production code, do not create tickets, and do not add scope.
- Then review what you changed against the other documents and scope.md.

**Hold the ticket for what must merge first.** Read code-design.md's "Waits for" line and check each item on the default branch now. For each one still unmerged, the ticket must not move on to Implement: a session started there can only wait, for hours, and then builds on a branch that has moved. Put it in the round of questions: make this ticket blocked by each ticket that owns the missing work, named by what the build needs from it, with the wait it saves as the benefit. Once the person agrees, link them with the ticket tool that makes one ticket block another. Auto progress then keeps the ticket here and moves it on once every blocker is finished in a Done column, so Implement starts on a default branch that has the work. Missing work that no ticket owns is a Follow-up finding, asked the same way. Record what is held under "Waits for" in design-review.md.

**Ask once, all together.** Use AskUserQuestion only for what is the person's, with every question in one round and the most shaping first, and apply the answers to the documents. When the verdict is "Not ready to build", ask whether to send the ticket back to the design workflow that owns the problem or to build with the named risk, and say which you recommend.

**Hold or concede.** When the design records a reason that shows its author had more context, concede in one line (it makes sense, either works) and drop the finding. When the design answers a different question than the one that matters, restate yours once, with the line that proves it, and ask what you might have missed. When the point is a convention shared across repositories, decide for consistency, and leave room to discuss an exception that has a good reason. When the point is only your preference, drop it. When the design is the wrong fix, do not pass it: an approved wrong fix gets built. The register throughout: a question that carries a concrete alternative, one restatement with evidence when misread, no escalation of tone, and a pass as soon as the mechanism is gone.

**Put first what the person should read.** At the head of "Decided for you", at most five: non-obvious tradeoffs, departures from existing patterns, hidden scope expansions, load-bearing assumptions, drift from the stated goal, each as what the design commits to and what it gives up, the one with the largest consequence if the person would have chosen otherwise first. They are decided, not asked, and they do not hold the verdict: pass a design once it is good enough to build, and state what remains. None is a good answer.

**Questions to ask here**

- Did I check that, or do I only believe it?
- Is this mine to change, or the person's?
- Is this only a preference?
- What does the simpler form give up, exactly?
- After my fix, do the documents still say the same thing?
- If the person reads only five lines, which five?
