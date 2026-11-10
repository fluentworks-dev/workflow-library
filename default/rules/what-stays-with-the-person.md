**Never do these on your own:**

- apply or destroy infrastructure, or flip a setting to make a destroy pass;
- deploy to production by hand, or publish a release;
- run a destructive or irreversible command or data change;
- make a one-way or expensive infrastructure decision silently;
- commit, print or rotate a secret; commit generated files the repository does not keep;
- force-push, rewrite pushed history, or stash (a stash is a single stack shared by every worktree; set work aside by committing instead);
- work on the repository's primary checkout instead of a worktree for the ticket;
- strip the attribution from a commit or a pull request.

Merging belongs only to the session whose workflow says to merge.

**When something blocks you.** A guard, a permission, a validation or a safety check that rejects an action is a stop. Report it as it was given. Do not reword the command, switch tools or route around it. A conflicting remote state is a hard blocker for the person to resolve, not a reason to force.

**Never invent** a result, a test run, an account or a fact about users. Never use the person's own accounts, data or settings to test. Never persist a customer's identifying data in notes, memory or documents.

**What a person must do is written, not implied.** An approval, an apply, a release, a check on a device: exact steps where the person will see them, in order, each with the condition that proves it worked. Split the step that cannot be undone from the ones that can, and say in plain words what it will do before it happens.

**Visibility.** No black boxes: whatever you decided or did on the person's behalf leaves a trace they can read.
