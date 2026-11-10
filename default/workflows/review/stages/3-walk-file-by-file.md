Go through the queue in order, one file at a time. Do not edit code during the walk: a finding is written down, verified, and fixed later, so that every fix is made against the whole picture.

For each file, note a verdict (clean, or the count of findings by severity), a one-line summary, and a Structure map of its symbols: `[+]` added, `[~]` changed, `[-]` removed. Read each file against three lenses.

**Correct**

- Follow the logic with the edge inputs: empty, one, many, the limit, just past the limit, invalid, absent.
- Follow every error path: what is raised, what is caught, what the caller sees, what is left behind.
- Run it twice and interrupt it once: a retried request, a replayed batch, two writers.
- Put old data and an old client against the new code.
- Where two writes can fail between them, ask whether the next run heals.
- An abnormal case is raised, not tolerated, overwritten or truncated. A wait or a timeout that is part of normal operation is not raised as a failure.

**Simple and clean** (Google's code review guide, Kent Beck's rules of simple design, Fowler's code smells and Clean Code apply; the repository's conventions win over all of them)

- Functions and types: one responsibility, one level of abstraction, few parameters, no boolean flag that switches behavior, no hidden side effect, early returns over deep nesting.
- Names, on new and changed lines only: full words, the unit and the kind in the name, the domain's word, one name per concept, functions as verb phrases.
- Readability: a newcomer can read the file top to bottom. No magic numbers or strings, no dead code, no commented-out code, no debug output.
- Structure: no ladder where a lookup table would do; no helper of a few lines with one caller; a block that must be read twice is extracted and named; no file holding one constant used once.
- Errors: validated where data enters, handled where they can be handled, never swallowed, with messages that name the rule and the value.
- Maintainability: the next change of the same kind has an obvious home.

**Consistent**

- The repository's pattern for this kind of thing, and the sibling repository's when one already does it. A new pattern is a finding unless it is needed, clearly better and will repeat.

### What the comments are about

Most review comments fall under typing and data modeling, abstraction and flatter data, naming, layering, configuration and CI, correctness, error handling, concurrency, hygiene, product semantics and performance. Security and tests are easy blind spots, covered in the next stage. The principles behind the rest, each with an example:

- A layer must not know its children's concerns: a shared file manager need not know anything about images; it only cares about files.
- Invariants live where every caller passes: enforcing in the manager makes the invariant hold for every caller, and the router just forwards.
- Flatter data, maps keyed by stable names, nothing stored that the structure determines: a list of addresses becomes a map keyed by the address name, such as `billing`; an extra namespace that wraps one level adds a model and makes the JSON deeper for nothing.
- Invalid documents unrepresentable: a union on the discriminator, where a flat payload means nothing stops a card payment from carrying a `bank_routing_number`.
- One response type per method: a method that only publishes an update has no knowledge of whether the workflow succeeded, so it does not report success.
- Configuration required, explicit, beside what it configures: settings that are always needed are non-nullable; the runtime environment variables are managed in one place, not some in the code and some in Terraform.
- Fail early and loudly, never overwrite silently: it is better to error out in this case so that someone can look at what is happening.
- Waits and timeouts are normal flow: fail the workflow only when there is a bug a developer should go and fix.
- Cleanup in `finally`, once; a lock wraps only the state mutation; do not wait for cleanup the caller does not need.
- In a store that serves history, old versions must stay readable: under `forbid`, any stored document version carrying an old key becomes unreadable.
- Reuse the organization's SDKs and primitives, and link the sibling repository that already does it: a shared `Address` model already exists, so reuse it instead of adding another `_ShippingAddress`.
- Names are global and functions are verbs: a name in a shared catalog is global to all the organization's data; function names are verbs or verb phrases because functions perform actions.
- Consistency across repositories beats local preference: when a choice is a preference, keep it consistent with the other repositories, and discuss an exception when there is a good reason for one.
- Return what the client needs, not a boolean; key artifacts by the platform's identifiers so they can be found and cleaned up.
- Hygiene: hard-coded strings are managed in an enum; a helper of a few lines used once is embedded where it is used; a file too small to be its own file joins its neighbour.

Verify a suspicion before it becomes a finding: run the code, write the five-line reproduction, read the installed library, query the test database. For example, a check run locally against a five-row table. A claim that spans files is verified now. A suspicion you cannot verify is dropped or put as a question, never stated as a fact.

Do not flag: taste the repository's conventions do not support; what a formatter or a linter enforces; docstrings, comments, types or names on lines the pull request did not change (a file-wide cleanup belongs in a separate pull request); edits to historical design documents; a clean file. Be willing to call a file clean with no concerns.

### In practice: Python services

Applies when the repository uses FastAPI and Pydantic with pre-commit.

- Walk models, managers, routes and workflow files. Expect to ask for a guard moved into the manager, a required discriminator, non-nullable configuration, a reused primitive.
- The generated contract is committed and regenerated by pre-commit: a stale one is a blocker. Consumers pin the SDK exactly, development build first, then the release.
- Test plan lines read like "`poetry run pytest tests/` (412 passed)" and "mypy passes (only errors that already existed remain)".

### In practice: TypeScript and React

Applies when the repository uses React with Playwright or vitest.

- The repository's template (Motivation, Approach, Result) wins; screenshots sit above the first heading; formatter fixes go in their own `style:` commits.
- Failures that already exist are separated: they are shown to reproduce identically on the unmodified parent commit. What a fake tenant cannot prove stays unchecked.

### In practice: Terraform and Terragrunt

Applies when the repository uses Terraform modules with Terragrunt units.

- The Test plan carries plan counts ("12 to add, 3 to change, 0 to destroy"; "must report no changes" after imports). A person applies. A Reviewer note carries the cost.
- Ask whether this is the smallest instance type that does the job in a sandbox, and whether it works in every cloud partition the repository deploys to.
- A rename of an applied resource is never a nit: renaming an applied resource is not a free convention cleanup.

### In practice: Spark data pipelines

Applies when the repository uses Databricks notebooks and Delta tables.

- CI validates the bundle only: pipeline tests run by hand and their counts go in the Test plan. Post-merge SQL goes in the description. A tag after merge deploys: minor for a new table or pipeline, patch otherwise.
- A name that enters the shared catalog is qualified by the system that owns it. Backfill logs and state databases in the diff are blockers.

### In practice: ROS 2 and robots

Applies when the repository uses ROS 2 lifecycle nodes.

- The repository's conventional commits (`feat(navigation):`) and its template with release notes for operators; features target `develop`, hotfixes `release`; `VERSION` is bumped by its rule; design documents are removed before the pull request opens.
- CI only builds: a run on the robot is the gate and stays unchecked until done. Defer to the robotics engineers on lifecycle and threading, and ask where each setting the change reads is configured on the robot.

### In practice: GraphQL

Applies when the repository uses GraphQL as a read-only aggregation layer.

- Every filter value that reaches SQL is bound, not quoted. CI runs the linter only, so test counts go in the Test plan. A performance fix carries the production measurement and what it does not solve.

### Questions to ask here

- Can these two be non-nullable?
- What do these abbreviations mean? Can the names be spelled out?
- Could a constant lookup table replace this if-else chain?
- Is this still used? If not, can it be removed?
- Would raising an exception be better than returning a feature that is not supported yet?
- What does this field mean?
- What happens when this runs twice?
