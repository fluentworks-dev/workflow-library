Read the ticket and its documents in order: scope.md, product-design.md and its design file, system-design.md, code-design.md, design-review.md. Read the ones the ticket has: a board without a System Design or Design Review column leaves no system-design.md or design-review.md, so build from the others and say in the pull request which one was missing. They are settled: do not redesign and do not ask again. Where design-review.md revised a design, the revision wins. When code-design.md is missing, do not write one: note the few tasks you will do and the files you will touch, and put that note in the pull request.

When code-design.md says no code changes are needed, there is nothing to build. Say so in three lines on the ticket (what the designs concluded, that no branch and no pull request were opened, what the person must do and how they will know it worked) and finish.

Read the repository's guidance file, the global constraints and the reference files code-design.md lists, and the code each task touches. Read only that.

**Worktree.** Work in a worktree off the latest default branch, on the branch code-design.md names, or on a slug of three or four words naming the scope. Continue the ticket's branch when one exists. Never work on the primary checkout.

**No baseline run.** You run each task's own test; CI runs the gate on the push, and Software Validation runs the whole set once with counts against the parent commit. When a test you add sits in a file that already fails on the default branch, say so in the pull request with the failure's name; fix it in its own first commit when the fix is small.

**Check the plan against the code before the first task:**

- Do the paths, names and signatures the tasks consume exist as written?
- Did the default branch move since the design? Follow the default branch and note the difference for the pull request.
- Is everything under code-design.md's "Waits for" on the default branch? When something is not, build nothing and never start on top of another ticket's branch. Report blocked, naming what is missing and its ticket, and when this ticket is not yet blocked by that ticket, propose the link in the same question.
- Is a task impossible as written, or do two tasks contradict each other? Stop and report it, like this:

```
The plan cannot be built as written.
Task 3 consumes `FileStore.attach(association_name=...)`. `src/stores/base.py:88` on the default
branch has no such parameter, and Task 2 does not add it.
Options: (1) add the parameter in Task 2: one line, and it matches the design's intent
[recommended]; (2) return the ticket to Code Design.
```

  When a repair keeps what the design promised, as in option 1, do it and record it under "Decided for you", also when it changes how the design gets there. When no repair keeps what a person can do or was promised, ask with AskUserQuestion.
- Is a load-bearing value missing from the source (an identifier, a limit, an endpoint)? Escalate; do not guess. A value that is not load-bearing gets a reasonable default with a `# verify:` comment, listed under "Decided for you".

### Questions to ask here

- Did you read the design review, or only the design?
- Does the file you are adding a test to already fail on the default branch?
- Is this branch off the latest default branch?
- Does that function still exist on the default branch?
- Where is this environment's setting defined? Ask, don't guess, when the environment is unknown.
- Is the plan wrong, or did you misread it?
