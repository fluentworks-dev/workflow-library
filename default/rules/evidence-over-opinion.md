Evidence over claims: run it, test it, observe it.

**Verify before you accept, decline or write.** Check a claim against the installed version, the real schema, a real engine, production-shaped data or the parent commit, and say what you checked. This is the register:

- "Verified against requests 2.32.3."
- "Verified against the full source (48,210 rows)."
- "Verified locally with a 5-row table."
- "Confirmed failing on the parent commit and passing here."
- "Mutation check: adding a `break` ... makes the test fail."

**Report only what you ran.** Give the command and the count ("312 passed", "64/64 pass", "12 to add, 3 to change, 0 to destroy"). Do not write a test plan the implementer did not actually run. A check you could not run stays visible as an unchecked item with the reason; it is never reported as passing and never dropped.

**Separate what already failed from what you broke.** Prove it on an untouched checkout and say so, for example: the 30 failures reproduce identically on the unmodified parent commit.

**Numbers have units and reasons.** Every freshness, limit, size, retention and timeout gets a number, a unit and a reason ("9 workers = 2 x vCPU + 1 for the 4-vCPU plan"; "about USD 900 per month for the message brokers"). When a number is chosen by feel, say so instead of dressing it up.

**Measure speed and cost** before and after, in the real environment, and state the limit of the fix: "cold 95 s to 8 s, warm 60 s to 0.9 s", and that this only stops the bleeding while the durable fix is something else, named. When a mitigation does not work, remove it the same day and fix the underlying cost.

**Diagnose before fixing.** Read the error, the trace or the history before changing anything. Five speculative fixes that are later reverted cost more than reading the trace once.

**Every change in a fix answers a problem you have shown.** This holds for code, a design, a document and an instruction alike. First see the problem yourself in the thing that has it, and record the evidence: the failing command and its output, a measurement with its number and unit, a screenshot at the size a person uses it. Then name, for each change, the observed problem it answers; in the pull request or hand-off this is a table of change, evidence, result. A change that answers nothing you observed ("might also help", "while I'm here", a guess at another cause, a preference of style) comes out of the fix; when it still seems worth doing, it becomes one line for a new ticket marked "Assumption:", with what would prove it. A problem your own fix created is not a reason to add more: take out the part that created it. After the fix, measure the same thing the same way and report before and after.

**Prose is part of the change.** Text that describes behavior the work no longer has is the most common defect reviewers find. Before you hand anything on, reread every comment, docstring, document sentence and pull request line you wrote against the final state, and fix what drifted.

**Never invent.** Never fabricate facts or requirements. Mark what you have not verified with the word "Assumption:" and say what it depends on.
