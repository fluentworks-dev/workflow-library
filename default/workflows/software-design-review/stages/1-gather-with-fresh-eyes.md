A design review is a check, not a critique: it removes what need not be built, fixes what is clear, and tells the person what the designs commit them to. It is not a second design.

**Stay outside the work.** This session did not write the designs, and a review is only worth the prompt it costs if it comes from outside the work. Be honest, not deferential, and arrive with the standard practice for each technology involved.

**Read, in this order.**

1. The ticket and scope.md, then the questions earlier sessions asked and the person's answers: those are decisions, and they are settled. Do not read the earlier sessions' summaries; they carry the authors' conclusions.
2. Before opening any design, write three notes: what a good design for this scope must contain (surfaces, stored data, contracts, rollout); a pre-mortem (assume the change shipped and failed, and write the two or three likeliest reasons); and, for each technology involved, the two or three things a specialist checks first (for a REST write, idempotency and status codes; for a migration, expand and contract and what a rollback does; for client state, one owner per piece of state; for anything concurrent, the race window).
3. product-design.md and its interactive design file, system-design.md, then code-design.md. Go through the person's path in the design file yourself; do not review the document's account of it. Note each document's status line and revision. A board without a System Design column leaves no system-design.md: review the system decisions where code-design.md makes them, and say so in your output.
4. The code the designs cite, the repository's guidance file, and one or two neighbouring implementations of the same kind.

**Check the facts the designs stand on.** For every claim a design relies on (a path, a name, existing behavior, what a check already does, which consumers exist, how a library behaves), check it against the default branch or the installed version and keep a row: the claim, what you checked it against, the result. A design built on a wrong fact is the first finding. When the default branch has moved since a design was written, the default branch is the fact.

**Check the three-line documents.** A design that says there was nothing to design is a claim: a system-design.md that says nothing changes beside a code-design.md that adds a column is a finding.

**Check the audience.** system-design.md and code-design.md are for builders in a known repository: file paths, class names, snippets, code before abstraction. A document for technical leaders who do not know the codebase is a different type: components by role and never by class name, seven sections, about 300 lines. A document that mixes the two serves neither.

**Choose the playbooks.** Note which "In practice" lists in your instructions apply to what the designs touch, and use only those.

**A second round.** When the ticket already carries a design-review.md, sort its findings into still relevant and addressed, then review what changed since, including the choices made in response to the first round: a fix can create the next finding.

**Decide whether there is anything to review.** When every design is a true three-line document, write design-review.md in its three-line form and finish. When a design that scope.md calls for is missing, do not write it: the verdict is "Not ready to build", naming the document and the line of scope.md that calls for it.

**Questions to ask here**

- What is this change for, in one line, and who is it for?
- What does this name mean? When a name explains nothing, find out what it is before judging it.
- Is that true on the default branch today, or was it true when the design was written?
- Which of these documents says there is nothing to design, and is that right?
- What would a specialist in this exact technology check first?
