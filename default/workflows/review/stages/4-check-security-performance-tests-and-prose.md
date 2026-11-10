Four more passes over the same queue. Each is its own pass because each is missed when it is folded into the reading of the logic.

**Secure.** Security is a category here on purpose: a review that reads only the logic rarely raises it. Then a high-severity finding from the automated reviewer (an administrator credential exposed to plan code that a pull request controls) is neither fixed nor rebutted and the arrangement is copied into a second repository; known gaps ship with a note instead of a block (a user-list endpoint without authentication, a secret delivered as a plain application setting); and filter values interpolated into SQL after quoting only are flagged by nobody. For every file that touches a boundary:

1. Who can call this, and what do they control? Is every new entry point authenticated, and authorized where every caller passes? Does a missing identity fail closed? Does the server decide who the caller is?
2. Does input reach a query, a shell, a path, a URL or markup? Quoting is not parameterizing; field names and sort keys need an allowlist.
3. Is a secret in code, a fixture, a build argument, a URL, plan output, state or a plain application setting? Can a production secret resolve to a development default? Does a token reach the browser?
4. Do logs and errors carry identifiers only? Does a denied read reveal existence? Does internal detail reach the user?
5. Does CI run this pull request's code with a credential that can write, deploy or administer? Are permissions minimal and tools pinned?
6. Does a value that selects an environment have a code default? Is a tenant, an application identifier, a host or a personal address hard-coded, and at least named in a comment?
7. Is an insecure path left dormant instead of deleted: an unused login route, a fallback secret, a debug endpoint?
8. Does a response give away more than the consumer needs?
9. Is a new dependency justified and pinned?

The list follows the OWASP Top 10, least privilege, and GitHub's guidance that untrusted pull request code never runs with secrets. A security finding is a blocker until shown otherwise; verify it by building the input that shows it. Every security finding, yours or the automated reviewer's, gets a fix or a verified reason in its thread. A gap that cannot be closed here is the person's risk to accept: ask, and record the answer in the description. Never copy an arrangement with an open finding into another repository.

**Performant.** Only where the path is hot or the data can grow:

- A call per item in a loop, a query per row, the same read repeated.
- A list, a queue, a cache or a log that grows without a bound.
- Work that blocks an event loop, a callback or the UI.
- A client or a token built per request that could be built once.
- A filter applied after the read that could be applied at the source.
- A size, a schedule or an instance larger than the need.

When a finding claims speed or cost, ask for the number before and after. Do not micro-optimize.

**Tested.** Read the tests last, for one thing: would they fail if the behavior broke?

- For each important test, name the change to the code that makes it fail. When you cannot, that is a finding.
- A stand-in that accepts any argument proves nothing: the test asserts the exact arguments and the exact type sent.
- A fix of a defect has a test that fails on the parent commit.
- A test that exercises a library and not this code is deleted, such as a test that checks gzip itself and not how the code uses gzip.
- A replayable operation is tested twice. Secrets are shown absent from captured logs.
- The Test plan in the description lists only what was run, with counts and the baseline, and leaves unchecked what was not.

**True.** Prose that disagrees with the code is among the most frequent reviewer findings, because assisted authoring multiplies prose and no test covers it: a comment that reads like an assistant justifying the reversal of a change it made is a typical case. Reread every comment, docstring, error message, description sentence and guidance line the pull request wrote or touched, against the code as it is now. Look for:

- A comment that narrates the change or defends a reversal. A comment states the constraint.
- A description of something the branch does not contain (a pin the branch never carried, modules and tests that never shipped).
- A README or guidance section the change made false; a link to a file the change removed. The guidance file and the README change in the same commit as the convention.
- A temporary pin with no bold flag and no unchecked box; a version not bumped by the repository's rule.
- Residue: a typo in anything durable (a persisted column named `null_excpetions` is now permanent), a template placeholder left in the description, a save-point subject, a second author identity.

**The recurring misses.** Before leaving this stage, sweep the diff once more for what reviewers keep finding, most frequent first:

1. Prose that disagrees with the code (the pass above).
2. A development pin or a version bump left at the end of the branch.
3. No evidence of a real run: the Test plan says what was run and where, with counts.
4. A test that exercises a library and not this code.
5. Rules of a domain that is not the author's. The lifecycle and threading rules of a robot stack, for example, get taught again and again in review: read the domain's guideline before judging.
6. A silent retry, or a success flag that is always true: both hide the fault from the operator.
7. A consumer of a removed route, parameter or default: search the sibling repositories. A removal is deprecated first.
8. Size inflated by committed artifacts, and working design documents left in the tree or still linked.

### Questions to ask here

- Is this the smallest instance type that does the job? A sandbox needs only the smallest.
- Does any new environment variable need adding to the Terraform configuration?
- Has this been run in the development environment to show it works?
- Who can call this without a token?
- Does this value end up in a log?
- Which change to the code makes this test fail?
- Does this comment still say what the code does?
