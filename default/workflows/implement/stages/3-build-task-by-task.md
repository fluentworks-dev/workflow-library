Take the tasks in order. Each is one loop of test-driven development, red before green:

1. Write the test the task names, in the repository's test convention.
2. Run it and read the failure. It must fail because the behavior is missing, not because of a typo or a missing import. A test that passes on its first run proves nothing: fix the test.
3. Write the least code that makes it pass, matching the code around it.
4. Run the test green. The type check, the lint and the gate set are not run here: CI runs them on the push and Software Validation runs them once with counts. Run one of them after a task only when the task changed a type or a contract a later task consumes.
5. Commit with the subject the plan gave. When that subject no longer describes the effect, write one that does. Stage with `git add -u` and name new files explicitly, so no scratch file, log or local state rides along.

**While building:**

- Every changed line traces to the task. Leave neighbouring code as it is.
- Names, comments and messages are in their finished form on the first pass: full words, units, the reason and not the mechanics.
- Comments in the finished form read like these: "Implemented without `BaseHTTPMiddleware` so the context variable set here stays visible to the route and to the exception handlers that run inside the same task."; "exclude_none keeps a blank association_name out of the wire payload entirely (not null), so the upstream service applies its own default." A module docstring points at its neighbors: "`AbacMiddleware` (see `src/abac/middleware.py`) turns the validated token's claims into a `CallerIdentity` once per request ... Managers never see any of this; they receive the resolved string."
- A comment cites its evidence: "observed on the orders and refunds topics with payloads that predate those fields". A retired definition may stay as a comment beside the live ones when its history belongs next to the fields ("Carry over the retired legacy_status entity block as a comment"); mark it retired.
- At a product boundary an error is a domain, a message and a remediation, and the last two read as a pair: "The name 'X' is already taken by another app." / "Pick a different app name." A misconfiguration fails in the first call that can see it, "instead of a cryptic `gh repo create` failure mid-deploy".
- When one identifier appears in two casings (`ApiClient` beside `APIClientConfigModel`), new code uses the casing the repository uses most, and the pull request says so.
- A stale comment is fixed in its own commit, which admits the error when there was one: the commit "Correct the AWS_REGION comment to the verified reason" says "the earlier wording was wrong". A commit that only fixes prose says what it fixes: "docs: describe order update trim behavior, not mechanics".
- A cross-field rule goes in the layer that has the context: "`OrderUpdate`/`ShipmentUpdate` carry no order-type context, so a field-level validator could not tell". A validator that can never run, because an earlier constraint fails first, is dead code: verify that locally, then remove it.
- A fix after a task's commit is a new commit named for what it fixes ("fix Row coercion in RecentOrdersLoader._load"). Nothing is folded into an earlier commit, and there are no `fixup!` commits.
- A rename gets its own commit. Code your change orphans is deleted in its own commit, whose body names what is deliberately kept.
- When the repository numbers its migrations, take the next free number from the latest default branch before pushing, and renumber when the default branch gains one.
- After three failed attempts at one problem, stop adding fixes. Read the whole trace, revert the speculative attempts, and question the approach against the design.
- A departure from a design that changes what a person can do or see (a control left out, a state dropped, a word a customer reads) is the person's: put it in one batch with any other such point, build the tasks that do not depend on it meanwhile, and build it after the answer. Any other departure is recorded with its reason under "Decided for you".
- Build in this session. When you do hand a task to another agent, send the task identifier, the plan's path and section, the branch, what the plan does not capture and the success criteria, and no pasted code.

**Tests, in detail:**

- Names are behavior sentences: `test_update_shipment_sets_parent_guards_write_with_etag`, `test_create_export_with_an_unreadable_source_store_returns_400_and_writes_no_export`, `test_get_order_keeps_its_own_404_text_for_not_found`.
- Judge each test against two imagined edits: if the behavior breaks, it must fail; if the internals are restructured with the same visible result, it must still pass. Prefer no new test over one that mostly tests mocks.
- Replace only the outermost seam. Patch the single function that sends a query and let the batching library run, and a test can prove that two concurrent lookups produce exactly one query. Write small real tables or files into a temporary directory and drive a command-line tool through its test runner.
- A stand-in has the real shape: it refuses what the real one refuses, and its error wording is copied from the real service.
- A test that walks a table replaces a convention: every route threads the access policy; every operation of a deprecated version is flagged deprecated; every member of an enumeration has a union variant.
- A parity suite runs one set of scenarios against each adapter. When porting, the old code is the specification, and the output is compared byte for byte for every entity.
- A replay test states its invariant in the docstring: `test_fallback_then_replay_keeps_rows_out_of_account_state`.
- A log-hygiene test proves both halves: `assert SECRET_TAG_VALUE not in caplog.text` and `assert "api_token" in caplog.text`.
- An existing test that breaks means your change is wrong, until the design says the behavior changes. Then edit the test and write "Behavior change in tests:" in the commit body. Otherwise stop and report. The rule has two faces: a builder following a plan does not edit existing tests, and a repository's own rule may read "Keep tests in sync with code changes; no approval is needed to modify a test ... When a change alters what a test asserts (a behavioral change), call it out in the commit message or PR". Both mean a behavior change is never hidden inside a test edit.
- One function per scenario; parametrization is rare, because a named function per scenario reads better than a table of cases. Test helpers are named for what they build.
- A test that only exercises a library is deleted: a test of gzip itself, and not of how the code uses gzip, goes.
- A legacy suite that no longer runs is removed once its scenarios are covered: the broken suites are removed, their cases translated into the suite that runs. Say so in the pull request.
- Wait for a condition, with a timeout and an error naming what you waited for. A fixed delay is right only for behavior that is itself timed, with a comment saying why that length.
- Code that only tests call lives in test utilities, never in production classes.

**Tooling and documents, as you go:**

- A generated contract (an API description, a client) is regenerated by the repository's hook, never edited by hand. A stale one fails the pull request.
- A suppression carries its rule code; a per-file ignore carries its reason. A suppression that hides a real finding in new code is not allowed: fix the finding.
- The guidance file holds commands, rules for after a change, the architecture, conventions with their reasons, terminology and gotchas; the README holds operations, runbooks and a status paragraph. When your change makes a sentence in either false (a port, a storage backend, a layout, a checklist step), fix it in the task that makes it false, and change both files in the same commit when a convention changes. Markdown that agents read is terse: "keep sentences as short as possible without losing information".
- A setup step that bit you goes into the guidance file now, with the error it prevents ("setup.py install_requires alone does not install tenacity during backend bringup").
- An operational script is idempotent and re-runnable, with `--dry-run` and bounded concurrency, safe beside the live service. One-off backfill code ships with the forward change and is deleted once it has run. What a script produces (tracker logs, state databases, data samples) is not committed: add it to `.gitignore` in the same change. Infrastructure repositories have no task runner; commands are documented verbatim in the README's "Operations" block.
- A repeatable check is shipped in the repository as an agent skill or a template: a smoke test of every route with expected status codes, a pull request template that forces thought about schema evolution ("Data back-fill requirements checked").

### Questions to ask here

- Did the test fail first? For the right reason?
- What is the point of the changes in this file? Are they necessary, or a workaround?
- Is this change relevant to the ticket? If not, remove it.
- What do these abbreviations mean? Spell the names out.
- Is this helper used once? Then inline it into the code.
- Can this if-else ladder be a constant lookup table?
- Why is the existing test changing? Did the behavior change on purpose?
- Three tries and still red. Is the approach wrong?
