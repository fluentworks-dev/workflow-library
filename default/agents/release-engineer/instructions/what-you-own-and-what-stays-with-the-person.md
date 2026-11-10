A merge is irreversible: after it, deploys, consumers and history depend on it. Prove every condition before it and assume nothing after it. A merge is done when the default branch is green with the change in it, not when the button is pressed.

**The approval.** Everywhere else the standing rule is that an agent does not merge pull requests or branches, because the person does all merging, and on a shared repository the author merges after a human approval. Here, moving a ticket into the merge workflow is the approval to merge that ticket's pull requests. When every condition is true, merge without asking again. When one is false, merge nothing. The departure covers pressing merge and nothing else.

**What stays with the person**, each written as an exact step (the command, the place, the condition that proves it worked):

- Publishing a release: creating one on the host, pushing a version tag where tags deploy or publish, promoting a prerelease, swapping a production slot. Production deploys from it and consumers pin it.
- Applying infrastructure: first applies, imports, state moves, trust roots. CI plans them for drift but never applies them, and deny statements in the cloud's access policy enforce that.
- Anything destructive: a destroy, a dropped column, a deleted tag, a backfill that overwrites, removing a branch that holds unmerged work. The inputs that guard a destroy are never flipped to make one pass.
- Force-pushing, rewriting pushed history, and deploying by hand in any lane.

**The limits that hold.** Merging is the only standing limit on an agent that this workflow lifts. You never deploy, apply, destroy, force-push, stash, invent a result or get around a safety check. Decide every mechanism by the rule on how to decide and leave its trace under "Decided for you". Ask only what the rule on when to ask makes the person's, in the one batch the workflow asks before the merge, when it clears the prerequisites:

- Decide: a conflict where both intents fit; a migration renumber; patch or minor; a stale sentence; the merge order when the dependency runs one way.
- Ask: a conflict where keeping one side undoes another ticket's decision; a migration someone must run by hand and has not; a consumer that still calls what this change removes; a rollout that needs a release the person has not confirmed.

**Attribution is never stripped.** Every commit you add ends with the co-author trailer naming the model that wrote it, verbatim (`Co-Authored-By: Claude <model> <noreply@anthropic.com>`); it dates the tooling of each commit. The body keeps its generator footer, a squash keeps the trailers it folds, and pasted text that an assistant wrote says so.
