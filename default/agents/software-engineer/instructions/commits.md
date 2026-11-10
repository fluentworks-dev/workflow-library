The repository's convention wins: always prioritize existing project patterns over personal preferences. Before the first commit read the last three months of the default branch, the pull request template and the repository's guidance file. The rules below are the default where the repository has none.

Subject:
- One capitalized imperative sentence of 50 to 72 characters that names the effect, not the file: "Reject a permission change that would remove the caller's own access".
- No type prefix, ticket key, period, task number, em dash or abbreviation ("new op types" is written "new operation types").
- A second, smaller change rides after a semicolon, never "and also": "Load feature flags from a file; rename staging to preview".
- "Topic: detail" for scaffolding, design revisions and review polish: "Spec: pin SDK 2.4.1, add the export retry design".
- Never written: save points ("save progress", "fix lint") and conventional prefixes ("feat: ..."). Use a prefix only where the repository does (`feat(billing):`).

Body:
- Wrapped prose at 72 columns: the failure as someone met it, the cause, the change, the measurement or proof. No bullets, headings or test plan.
- Add when true: a measurement ("cold 48.2s -> 3.1s, warm 40.7s -> 0.6s"); "Behavior change in tests: ..."; what was left alone ("the YAML parser stays: the import job still uses it."); who reported it; a corrected claim ("the earlier wording was wrong").
- End with the co-author trailer the session gives, model name verbatim.
- This follows Chris Beams's seven rules and GitHub's `Co-authored-by` convention, and departs from Conventional Commits on purpose.

An example commit. Failure, cause and change in six lines, with the exact strings a reader can search for:

```
List orders through the v2 API so the Orders page shows every store

v1 list_orders declares store_id as Query("") and forwards the empty
string as a filter; the order service turns that into a
store_id == "" filter that matches nothing, so the Orders page always
showed "No orders found." in staging. v2 treats a missing store_id as
all stores, so the page now calls list_orders_v2 and passes store_id
only when the caller supplied one.

<the co-author trailer, exactly as the session gives it>
```

Shape of a branch:
- One commit per task with its tests; you run that task's own test, and CI runs the gate on the push. Fix-ups and review fixes are plain commits on top: never amend pushed work, never `fixup!`.
- Its own commit each: a rename (called out in the description), formatting, deletion of what your change orphaned (say what is kept), and a stale comment.
- One commit per review round, its subject enumerating the points: "Address Copilot review: retry limit, null handling, field descriptions, empty pages".
- Never commit logs, state databases, data samples or scratch files (ignore them in the same pull request), generated files the repository does not keep, or secrets. A working design document that was committed is removed in its own commit before merge, with no link left to it; one the repository keeps by policy is the first commit, and each revision of it is its own commit.
