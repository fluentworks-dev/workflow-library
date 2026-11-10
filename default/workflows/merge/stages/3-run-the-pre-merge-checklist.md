Every item is true at the current head, or the merge does not happen. Each is on the list because it is easy to miss. Prove each with a command; never take the description's word for it.

| # | Item | True when | Prove it |
|---|---|---|---|
| 1 | Pins and version | every development-build pin is swapped for a release; the version file is bumped by the repository's rule | search manifests and lock files for `dev`, `rc`, `alpha`, `beta`, a git URL, a branch name, a local path; the release exists and contains the commit it should |
| 2 | Working documents | design documents and plans are removed in their own commit, or kept by the repository's stated policy; no link points at a deleted one | list the documentation paths in the diff; search the repository for each removed path |
| 3 | Prose | every comment, docstring, body sentence and guidance-file line matches the final diff; stale text was fixed in its own commit | reread them against the diff, the title and the body first |
| 4 | Test plan | only commands that were run, with counts and baselines; boxes unchecked and visible for what could not be run | the counts Validation wrote, rerun at this head only for what the update touched; a count taken before the update describes a branch that no longer exists |
| 5 | Committed artifacts | no tracker logs, state databases, data samples or generated files, unless the repository commits them by policy | read the file list before the line count |
| 6 | Consumers | for any removed route, parameter or default, sibling repositories were searched and the removal was deprecated first | the repositories searched, the names, the callers found |
| 7 | Security findings | each is answered in its thread with a fix or a verified reason; none is silent | read every thread, the automated reviewer's too |
| 8 | Base, branch, title | the right base; the branch is a slug of the scope; the title is the squash subject | a pushed branch with a poor name is noted, never renamed |
| 9 | Applied infrastructure | a rename or move of applied infrastructure had its naming settled in the design and proven by offline tests before the first apply | the design's naming decision; the tests' result |
| 10 | Right pull request, green and answered | it is the one the ticket names; required checks are green at this head; tests that run only by hand were run by Validation, and again here when the update touched them; every thread is resolved, the non-blocking ones too; no consumer's objection is open | the host's state at this head |
| 11 | Not held | not a draft; no standing "Do not merge yet"; no status word in the title | the body and the title |
| 12 | Rollout order | each migration and setting arrives before the code that needs it; each removal lands after its last reader | the migration files and the rollout section, read against the code |
| 13 | Prerequisites | every item on the prerequisites list is verified or done | the next stage |
| 14 | Residue | the checks below pass | below |
| 15 | Project memory | every fact this ticket's sessions learned is in the project's memory, once, in its current form; nothing a later ticket would need lives only in a summary, a comment or a pull request | the ticket's summaries and comments against the memory; save what is missing, consolidate duplicates |

Why these: a development prerelease that merges has to be fixed after the fact, and reviewers end up asking for the production version before merging; dangling links to deleted documents stay in a README; committed artifacts, more than scope, make the giant pull requests; a high-severity finding left neither fixed nor rebutted gets copied, with its arrangement, into a second repository; a discussion with a consumer gets merged over. Item 9 is a counter-measure that is rarely a habit; apply it anyway.

Residue, each of which is easy to leave behind:

- One author identity on the branch (`git log <base>..HEAD --format='%an <%ae>'`), not a machine-local hostname address. Never rewrite history; note a stray one.
- No typo in a name that becomes durable at the merge: the title, a tag, a migration, a new column, route or setting. A column named `null_excpetions` that is acknowledged and deferred stays that way: after the merge a rename is a contract change.
- No template placeholder or empty heading left in the body.
- No other open draft of this ticket; close each with one sentence and keep its branch.
- Nothing live pins this branch or an unmerged head.
- No release tag already on the commit you would propose one for (`git tag --points-at <commit>`).
- Anything you paste that an assistant wrote says so.

What to do with what inflates the diff: a tracker log or a state database is removed and its pattern added to `.gitignore` in the same commit; a data sample is removed, and a fixture is the smallest hand-written case, never a copy of real data; a generated file the repository keeps is regenerated at this head, and the body says how much of the line count it is; a script that ran once goes; foreign commits mean a wrong base.

Fix what is false in this order, each kind of file change in its own commit that follows the repository's subject form and ends with the co-author trailer:

1. Stale prose: comments, docstrings, the guidance file, the README.
2. Working documents removed, and the links to them.
3. Stray files removed, with their ignore pattern.
4. The title and the body corrected to the final state (an edit on the host, not a commit): the blocked mark gone, the alert removed once its prerequisites are met, the `Related PRs` states updated, the counts current.
5. The version bumped by the repository's rule.
6. Development pins swapped for releases, as the last commit.

The body's shape, when the repository has no template of its own:

```
## Changes            (or ## What changed)
- `symbol or file`, then the behavior, with the reason folded into the sentence
## Test plan
- `exact command`: 126 passed, 1 skipped (baseline before this branch: 114 passed, 1 skipped)
- manual verification steps with what was observed
- [ ] unchecked boxes for deploy-time or environment steps that could not be run
## Related PRs        (when more than one repository)
- Requires: [repository N](url) (merged, released in `v0.5.3`)
- Required by: [repository N](url) (needs the TypeScript SDK this pull request publishes on merge)
- Merge order: repository-a N (done), then this pull request, then repository-b N.
<the generator footer>
```

A body, filled, for a backend that swaps its pin to a release:

```
## Changes
- `routers/v2/files.py`: attach accepts an optional `association_name`, so one order can carry several files of the same kind.
- Pins `service-sdk` 0.5.3 (was the development build 0.0.1.dev68).
- **Breaking**: none. A request without the field behaves as before.

## Test plan
- [x] `poetry run pytest tests/`: 136 passed, 1 skipped (baseline on the default branch: 131 passed, 1 skipped)
- [x] `pre-commit run --all-files`: passed; `openapi.json` regenerated with no diff
- [ ] Staging: upload through the web application after the deploy (needs the deployed consumer)

## Related PRs
- Requires: [service pull request 12](url) (merged, released in `v0.5.3`)
- Required by: [web application pull request 8](url) (needs the TypeScript SDK this pull request publishes on merge)
- Merge order: service 12 (done), then this pull request, then web application 8.
```

Titles, before and after:

| Before | After | What changed |
|---|---|---|
| "[BLOCKED on <upstream> PR N] Tag access errors" | "Show the upstream reason when a tag write is denied" | the status word is gone; the title names the effect |
| "Update upload code and also fix tests" | "Upload report files with a presigned PUT; keep the multipart path for large files" | the second change rides after a semicolon |
| "Add new ord types" | "Add refund and exchange order types" | the abbreviation is spelled out |
| "feat(PROJ-123): add refund and exchange order types to frontend" | "Add refund and exchange order types to the frontend [PROJ-123]" | no prefix where the repository uses none; the key trails |

In practice: applies when the repository ships software to robots or other devices and an operator edits each device's configuration. A breaking-change note an operator can act on:

```
### Breaking changes
`settings.toml` on every device needs one new key before upgrading to 9.2.0:

    [cloud.client]
    client_id = "<the registration for this device's environment>"

Without it the node fails at configure with "cloud.client.client_id is required".
The staging value ships in `templates/settings.toml`.
```

Commit subjects for these fixes, for example: "Correct the AWS_REGION comment to the verified reason", with a body that admits "the earlier wording was wrong"; "Remove the planning document from the branch"; "Pin the SDK to 0.0.1.dev97" while the upstream is open. The release pin, as the last commit:

```
Pin service-sdk to the 0.5.3 release

0.5.3 is the first release that contains the association name on attach.
Replaces the development build 0.0.1.dev68 that this branch pinned while
the upstream pull request was open. 0.5.2 was tagged before that pull
request merged, so it does not carry the field.

Co-Authored-By: Claude <model> <noreply@anthropic.com>
```

In practice: applies when the repository uses TypeScript with npm or yarn. The same swap in `package.json`, with the lock file regenerated in the same commit: `"@scope/backend-sdk": "0.0.1-dev.90"` becomes `"@scope/backend-sdk": "0.7.0"`, never `"^0.7.0"`.

Clearing the automated reviewer's threads: one verdict word per thread ("Fixed in [hash]", "Resolved", "Declining", "Not a bug", "Not applicable", "Keeping", "Deferred", "Superseded", "No longer applicable", "Partly"); one commit named for the round ("Address the automated review: ANSI configuration, null blocks, struct descriptions, nullable array elements"); and one triage comment: "Went through each automated reviewer comment and checked it against the current head: 4 still relevant, the rest now addressed by the recent commits."

What already failed is stated, never hidden: "mypy passes (only pre-existing errors remain), 21 errors, all pre-existing"; "Mypy hook skipped: its 2 errors pre-exist on `main` (verified against an unmodified checkout)"; "the two pre-existing warnings in `useAssetUpload.tsx`". A diff swollen by renames gets a review guide: "Most of the 76 files only carry the renames listed under **Renames** below".

Two kinds of comment. The stale kind: lines that justify a change which was then reverted; they read like an assistant justifying itself, and they go. The durable kind names the constraint and the counterfactual: "Use the SDK's native retry for the blob PUT ... Re-using tenacity on top would force full re-uploads and stack backoff past the SAS validity window".

Finish what was left open. Anything the code review or the validation left open that can be done here is done now, on the branch. For each unchecked box: run it now when you can; leave it unchecked, with the reason, when only a deployed environment, a device or the person can. Such a box is worded like this: "Staging: sign-in round trip ... not testable locally with a fake tenant"; "Test on the device in offline mode".

A fix at this stage is small and inside this repository. A finding that would change behavior others rely on, or that needs a redesign, is not yours to make at the merge: put it in the batch of the next stage, recommendation first: whether the ticket goes back. An item you cannot make true is Blocked: stop, and ask with the item, what you saw and the options.

After the fixes, rerun the related checks, correct the counts, and wait for the checks at the new head. Write the result as the first section of the merge notes, one row for each of the 15 items, with the result True, Fixed in a hash, Not applicable or Blocked:

```
### Pre-merge checklist (head 3f2a9c1)
| # | Item | Result | Evidence |
|---|---|---|---|
| 1 | Pins and version | Fixed in 3f2a9c1 | `service-sdk` 0.0.1.dev68 swapped for 0.5.3 as the last commit; `git tag --contains` shows v0.5.3 holds the upstream merge; no version file here |
| 3 | Prose | Fixed in 7d6c5b4 | the README still said uploads go to one account |
| 4 | Test plan | Fixed | rerun at this head: `poetry run pytest tests/`: 136 passed, 1 skipped (baseline on the default branch: 131 passed, 1 skipped) |
| 6 | Consumers | True | removes nothing; adds one optional request field |
| 7 | Security findings | True | one finding from the automated reviewer, answered in its thread with the test that pins it |
| 9 | Applied infrastructure | Not applicable | no infrastructure in this repository |
| 11 | Not held | Fixed | the "Do not merge yet" alert removed: the upstream release exists |
```

**Questions to ask here**

- Is anything still pinned to a development build? Swap it for a released SDK version before merging.
- What is the point of the changes in this file? Are they necessary?
- Is the design document still on the branch? Does anything link to it?
- Does the test plan list only what was run? What was the count before this branch?
- Does every sentence in the description still describe the code?
- Is this still used? If not, can it be removed? Who else calls it?
- Is this required, or optional and better simplified for now?
- Is there a finding nobody answered?
