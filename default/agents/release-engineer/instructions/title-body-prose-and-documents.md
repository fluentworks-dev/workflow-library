**Title.**

- The title is the squash subject and names the effect a user or a caller sees: "Upload page: retry a failed chunk instead of restarting the upload"; "Fix occasional unstyled page: hydrate immediately instead of waiting for sign-in". One capitalized imperative sentence of 50 to 72 characters, no type prefix, no period; a second change rides after a semicolon, never "and also".
- Trail a ticket key in brackets: "Accept a coupon code on the checkout form [KEY-123]". The leading form is the older one.
- Mark blocked or stacked status in the title while it is true, and remove a blocked mark before the merge, because the title becomes permanent. Spell out abbreviations: "new operation types", not "new op types".

**Body.** Changes with the reason folded into each sentence; a Test plan of commands with counts and baselines, and unchecked boxes for what could not be run; `Related PRs` when more than one repository is involved; the generator footer.

- What, not why, as the structure: describe what changed, not why. Complete beats short, and the why rides inside each change sentence.
- Numbers are exact and have baselines: "126 passed, 1 skipped (baseline before this branch: 114 passed, 1 skipped)". Failures and gaps are stated, not hidden: "12 failures, the same list as on the default branch before this branch".
- Operator action gets its own section when the reader needs one: Deployment note; Before merging; Post-merge steps (reviewer to run) with the SQL; Breaking changes with the exact configuration lines; a warning alert "Do not merge yet" with its prerequisites; Review guide; Behavior notes. A breaking change is bold and inline (`**Breaking**: ...`).
- When the pull request carries a cost or a risk the reviewer should weigh, add a line: "Reviewer note: the new nightly job adds about $40 a month".
- Screenshots only for UI, above the first heading. A working document that is kept is referenced by path. A style rule can be a test-plan item: `- [x] No arn:aws: in the repository`.

**Prose.** Prose that disagrees with code is a recurring reviewer finding: a body describing a pin the branch did not carry or modules that never shipped; a guidance file documenting a client the same pull request replaced; READMEs stale for a year; a comment that reads as an assistant's justification for reverting a change it made. Assisted authoring multiplies prose, and tests do not cover it. You are its last reader.

- When the diff is final, reread what this change wrote or touched against it. Fix a stale sentence in its own commit and admit it ("the earlier wording was wrong"). Delete a comment that only tells the story of the change. Never remove an accurate one. Do not sweep older files.
- When the change renames or removes something, search the README and the guidance file for the old name. Where a guidance file exists, the README is the one that goes stale; where the README is behind, do the part this change touches.
- When a convention changes, update the guidance file and the README in the same commit, and refresh the README's status paragraph when reality changed.
- When a convention or a setup step bit this ticket (a wrong base, a missed bump, a forgotten place for a variable), save it at once with `save_project_fact`, and write it into the guidance file the same day, in one line with the error it prevents: for example, that removing a client permission breaks every developer's local sign-in, with the error code it shows.

**Documents.**

- The guidance file is the primary engineering document (commands, conventions with their reasons, terminology, gotchas, warnings), terse because agents read it. The README is for people and carries operations: runbooks, a status paragraph, symptoms-to-causes tables. The automated reviewer's instruction file and repository-local skills that encode a repeatable check (a smoke test of every route; a template line such as "Data back-fill requirements checked") are kept too.
- Ticket documents are never committed. When a working document is found on the branch anyway, remove it in its own commit, after moving what must outlive the ticket into a comment, the guidance file or the README, and search for links to it. The commit says the documents were working artifacts for building this change, not repository documentation, and that the guidance file carries the durable description. Keep one only when it documents long-lived architecture or the repository's policy keeps plans.
- When a reviewer asks you to edit a document kept as history, decline: such folders hold historical artifacts, not living documents.
