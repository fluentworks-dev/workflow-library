Find the ticket's pull requests from its branch and its comments. Read, in this order, the ones that exist: scope.md, product-design.md and its design file, system-design.md, code-design.md, design-review.md, the pull request description with its test plan, the code review summary comment, and the questions earlier sessions asked with their answers. What they settle is settled. A board made from an older template has no System Design or Design Review column; when system-design.md or design-review.md is missing, gather the promises from the rest and say in the validation summary which document was missing.

Write the list of promises, numbered, one line each, with the source:

- every outcome in scope.md that proves the ticket done;
- every state and wording a person can meet in product-design.md;
- every contract, limit, failure behavior and rollout step in system-design.md;
- every verification step in code-design.md;
- every unchecked box in the pull request's test plan, and everything the code review left open;
- every consumer of a contract the change touches.

```
O1 (scope.md, outcome 1): a person can type a name for each uploaded file.
O2 (system-design.md, contracts): a blank name is left out of the request, so the upstream default applies.
O3 (product-design.md, state "too long"): a name over 80 characters is refused with the server's sentence.
O4 (scope.md, out of scope): callers that send no name keep working.
O5 (pull request test plan, unchecked): "Staging: upload from the packaged application".
```

Prepare the ground:

1. Bring the branch up to date with the default branch the way the repository's guidance file says, never with a forced push. Resolve conflicts by keeping both intents: what merged is settled, so fit this change around it, and renumber a migration whose number the default branch already took. Ask only when the two sides cannot both be kept. Test the result, not the old branch.
2. Read how the repository is run and checked: the guidance file, the README's operations, the workflow files. Note what the gate runs and what it does not.
3. Make a second worktree, untouched, on the parent commit: the commit of the default branch that the updated branch now contains. The stage "Run it live" runs the whole check set there once beside the branch head; it is the pipeline's only baseline, and without it you cannot separate failures that already existed from new ones.
4. Read the diff to learn what it touches and where it could break. The code was reviewed; do not review it again.

When nothing in the change can be run (only prose changed), post the validation summary in three lines and finish:

```
## Validation summary
**Result:** nothing to run: the change edits prose only (the README and two comments).
Checked: the three commands quoted in the README run as written, and both links resolve.
```

### Questions to ask here

- What did we promise? One line each.
- What is the point of the changes in this file? Are they necessary?
- Which boxes in the test plan are still unchecked, and why?
- What already fails on the parent commit? The same list, or only the same count?
- What does the gate run here: the tests, or only a build?
- Who else calls this? Which consumer breaks first if it is wrong?
