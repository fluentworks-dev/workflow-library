**One story across four documents.** Compare what the documents share, cell by cell; when the ticket has no system-design.md, compare the ones it has:

| Shared thing | Must match across |
|---|---|
| Names of concepts, fields, routes, settings, states | All four documents |
| States and their wording | product-design.md and the types in code-design.md |
| Data each surface shows or sends | product-design.md and the contracts in system-design.md |
| Numbers: limits, sizes, timeouts, retention | Every document that states them |
| Order: rollout steps, merge order, task order | system-design.md and code-design.md |
| What is out, the anti-goals and the seams | scope.md and every design |

Every state in the product design has data behind it and a task that builds it. Every contract has a task that owns it. Every rollout step is a task or an exact step for the person. A disagreement is a finding in the document that is wrong; when you cannot tell which, scope.md and the person's answers decide.

**Open questions addressed to this review.** Answer every one a design lists: with a fix, with a fact you checked, or as an item for the person.

**Standard practice.** For each technology you noted in the first stage, compare the design with its standard practice. A departure with a written reason stands; one without is a finding.

**Sweep the blind spots.** These are twelve places where this way of working tends to drift. Check each and keep a result for each.

1. **Prose that disagrees with code,** a frequent finding: assisted writing multiplies prose, and prose has no tests. Every example, name, number and path matches the model and the code. The READMEs, guidance files and comments the change makes false are in the file map and change in the same commit. A comment the design specifies explains the code as it will be, never its history, such as a justification for reverting an earlier change. Nothing in the repository links to a ticket document. A closing task rereads all prose against the final diff.
2. **Thin review.** Approvals are often silent, or carry a pasted list of critical items that nobody follows up. Read every section and keep a result per lens. Never "ready" beside an open blocker. A consumer's unresolved objection goes to the person. For a part outside your home ground (frontend component internals, visualization, machine control), review what does not depend on the domain, and write what you did not judge and who should.
3. **Security designed well and reviewed rarely.** Review comments seldom raise it, and an unanswered finding (an administrator credential exposed to plan code a pull request controls) gets copied into the next repository. Every finding gets a fix or a verified reason, and none is copied forward. Defaults can silently point unconfigured environments at development; as a counter-measure, and not as established practice, have a new service carry its sandbox values in an example environment file instead of defaults in code.
4. **The release tail.** A development prerelease merges, a version bump breaks the repository's hotfix rule because nobody read it, two tags land on one commit. Each development pin has a swap as the last task, flagged in bold with an unchecked item. The version change quotes the repository's rule. One tag per commit.
5. **Size.** It comes from committed tracker logs, state databases, samples and generated files, and from pull requests on the wrong base carrying hundreds of foreign commits. The base branch is named from the guidance file. None of those artifacts is in the file map, and the ignore rule ships with the script that writes them. A split into a core part and a part for tests and documentation says whether each passes its checks alone.
6. **Churn.** Each new naming scheme for applied resources costs state moves, and a word such as "archive" can change meaning several times inside one change. Every name and meaning that will be applied, stored or published is decided here, with what it beat. A fix for an intermittent failure starts from a trace, not from guesses.
7. **Tests that do not run or do not bite.** Suites run outside CI, a module fetches a secret at import, and fakes that accept any argument miss a client signature change that then fails in production. Each outcome's proof runs where merges are gated, or the design says plainly that no gate exists. Nothing reads a secret or opens a connection at import. Fakes are strict and built from real object types. What is not tested is an unchecked item.
8. **Utility code below the bar:** scripts and one-off pages with SQL built from strings, bare exceptions, hard-coded paths and a retry of every exception. Do not sweep old lines, and design no new ones.
9. **Rules applied unevenly:** a stated environment code that no real name uses. Stated conventions match the repository as it is, and code-design.md's global constraints carry them.
10. **Domain contracts outside your core.** Lifecycle and threading rules get taught again change after change, and requests declined at review get built later by the domain engineers. Check the domain's guideline first, defer quickly, and take the expert's request now.
11. **Residue:** a persisted column named `recieved_at`, a live account running from an unmerged branch, placeholders in drafts. Spell every name that will be persisted or published. Leave no placeholder or "TBD".
12. **The conditions before a merge hold by construction:** pins swapped and the version rule followed; no working document, and no link to one, in the repository; prose reread; a test plan of commands that will really be run, with counts and the state before the change; no stray artifacts; consumers of anything removed searched for, and removals deprecated first; every security finding answered; the right base branch, a slug for the branch name, the squash subject as the title; applied names settled and proven offline.

**Anything odd.** Note what looks odd even when you cannot name the rule it breaks, then check it and keep it or drop it. Usually worth a second look: a number with no unit; a flag beside a flag; a boolean return; a sleep; a retry around a write that is not repeatable; a cache with no size or lifetime; a catch of everything away from a process boundary; "temporary", "for now" or "manually" with no owner; a step with no check; two names that differ by one letter. When the change has a quality no lens covered (ISO/IEC 25010 lists them; portability to another cloud partition is one), judge it here.

**Questions to ask here**

- Do the documents call this the same thing everywhere?
- Is the development build swapped for a release, and is that the last task?
- Is anything going to be committed that should not be: logs, state files, samples?
- Who answered the security finding?
- Does CI run these tests, or do we?
- Is that true in every environment, or only in the one it was tried in?
