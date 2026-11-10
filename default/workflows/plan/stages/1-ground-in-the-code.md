**First, scope.md's "Design workflows" line.** It is a decision. When it says Code Design has nothing to do, read the ticket and the earlier documents, save code-design.md in the three lines below and finish, without cloning or running anything, unless those documents name code that changes; then design that only, and say in the document what scope.md missed.

Read, in this order:

1. The ticket, its comments and every ticket document: scope.md, product-design.md, system-design.md. Read the ones that exist; a board without a System Design column has no system-design.md, and then you say in your summary that you designed from scope.md, product-design.md and the code alone. Read the questions earlier sessions asked and the answers given. What they settle is settled.
2. Each repository scope.md names: its guidance file whole, then the code the change touches, then one or two existing implementations of the same kind with their tests.
3. The lint, format, type-check and pre-commit configuration, and the CI workflow. Write down which checks exist and which of them gate a merge.

Keep grounding notes with file and line for every claim you will rely on. They become "Facts the builder can rely on" in code-design.md, each marked with where it was verified ("verified on `origin/main`, `src/stores/base.py:88`"). A fact you could not verify is written "Assumption:" with what it depends on.

Write down the repository's patterns that apply: how it layers code, names things, handles errors, tests, and writes commits and pull requests. These become the global constraints of the plan.

When code you will change looks deliberate, read the commits that added it. Record what must be preserved, what may change and what to avoid.

Do not run the check set: Software Validation runs it once, with counts against the parent commit, and that is the pipeline's only baseline. Write down the command for each check and which of them gate a merge; the global constraints carry them.

Verify the facts the earlier documents rely on. When system-design.md or product-design.md names a path, a function, an endpoint or a behavior, open it. A design built on a wrong fact goes under "Open questions for the Software Design Review"; do not design around it quietly. When the default branch contradicts a document because other work merged, follow the default branch and note the difference.

Then decide how much code design there is to do:

- **No code changes** (a setting changed, a manual step, documentation only): save code-design.md in three lines and finish.

```
Status: final. No code design is needed.
The change is one value in `deploy/settings.staging.yaml` (the session timeout, 30 to 60 minutes); no code reads it differently.
Verification: after the person applies the setting, the staging sign-in still succeeds.
```

- **One or two files with an obvious approach**: say so, keep "Model the concepts" and "Design the interfaces" to a paragraph each, and go to the tasks.
- **Anything else**: every stage in full.

Ask nothing in this stage. A technical question is answered from the code. A product question that scope.md does not settle is asked later, once, with AskUserQuestion, and only if the design cannot proceed without the answer.

### Questions to ask here

- Which file already does this? Did you read its tests?
- What is the point of the changes in this file? Are they necessary?
- Is there a model for this already? An existing `Address` model may serve instead of another `_ShippingLocation`.
- What does CI actually run on a pull request here? Only lint?
- Why is this guard here? Who added it, and for what?
- Is that fact verified on the default branch, or is it from memory?
