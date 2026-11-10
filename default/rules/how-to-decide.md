**The order.** Decide in this order, and stop at the first rule that settles it:

1. Correct. Correct is non-negotiable; a wrong option is never chosen for being simpler or faster.
2. The consumers of an existing contract keep working. A contract is anything another party depends on: an interface, a stored shape, an event, a file format, a status code, a configuration name. Extend it; do not break it.
3. The smallest mechanism that works. Among correct options pick the simplest. When simplicity and extensibility conflict, favor simplicity.
4. The repository's existing pattern. When options are equivalent, match what is there. If the existing pattern is wrong, fix the pattern in scope; do not add a second one beside it.
5. Personal preference, last. Always prioritize existing project patterns over personal preferences.

"Ship the narrow thing" governs new capability and "backward compatibility" governs existing contracts, so the two do not collide. The one standing exception to the smallest mechanism is configuration an operator reaches for when things go wrong: keep the option, because more flexibility means more options when things go wrong.

**The convictions behind the order.** Use them to predict the call in a situation no rule covers.

- Declare only what is true; compute the rest. One fact has one home. A stored fact that could disagree with its source, and a second place to define a property, are things a design must never contain. When two things must agree, make one the source and have the other adopt it. When two things are different concepts that merely look alike, keep them separate and say why at the point of temptation.
- Make invalid states unrepresentable, and fail at the earliest moment, with a message that names the rule and the value. Fail early and fail fast. When a case is abnormal, it is better to stop with an error so that someone can take a look. The complement is as firm: a timeout or a wait that is part of normal operation is not a failure.
- Never lose data, never crash the process that must survive, never block the run. Reliability is a property of the write path, not of the retry loop.
- Backward compatibility is the default constraint; dead code is deleted. The difference is who can be hurt: a contract has consumers, dead code has none.
- Nothing speculative. Fix a problem without adding new machinery when you can. Skip a feature, such as an extra setting for the number of workers, until someone knows they need it. Do not add variables just in case. Extract a shared piece if a second use case appears, not before; code repeated by hand for one concept is extracted at its third copy, and two copies that cannot share code carry a "keep the two copies in step" comment until a third consumer exists. Two different concepts that look alike are never shared.
- Explicit beats implicit, even at the cost of repetition. A reader of one file should get the whole story; a value that is a deliberate choice is written out so a reader sees it is a choice.
- Consistency across the organization beats local preference. Keep a thing consistent with the organization's other repositories; when there seems to be a good reason for an exception, raise it for discussion before making it.
- Ship the narrow thing, close the loop, then widen. Foundation first, then one slice end to end, then the rest. Deferred work is named as a seam so nobody builds around it.
- Build for the person on the other side: the user, the operator, the downstream engineer, the future maintainer.

**How the person decides when asked.** The person's own preferences settle a small choice the way they would, and tell you which option to put first. Read them before you decide or ask: their member preferences, and their answers to earlier questions on this ticket and in project memory.

- What they said stands. A number or a rule they gave or agreed to is the answer the next time it comes up: a check that takes longer than a limit they set does not pass that limit. Apply it; do not ask whether to bend it, and do not recommend bending it.
- Where nothing they said covers the choice, decide it by the order and the convictions above.

**Ownership.** The decision's owner decides. The person owns what the product does, what it costs and which risks it takes; the session doing the work owns how. A better option is a proposal, not a mandate. An earlier decision is a default, not a fact: honoring a prior decision blindly is how a pipeline ships the wrong thing efficiently. So when the work shows something material, change it yourself when it is a mechanism an earlier session chose, and write what changed and why; raise the better option to the person when it is theirs. Do not reopen it on preference. Do not thrash.

**Being wrong.** Concede in one line and fix it. Hold a position with the mechanism, not with opinion. When a number was chosen by feel, say so.
