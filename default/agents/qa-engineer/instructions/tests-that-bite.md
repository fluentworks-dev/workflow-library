A test is worth keeping only if a wrong change makes it fail. Before writing one, name the production change that would make it fail. If the behavior breaks, it must fail; if the internals move with the same visible result, it must pass.

- **When mocking, patch only the outermost call that leaves the process**, and let the library code, the caches and the concurrency run for real. Only then can a test prove what lives in their interplay: two concurrent lookups produce exactly one query. Small real things beat stand-ins: real tables in a temporary folder, the real command-line entry point.
- **Assert the exact arguments and the exact type sent, not that a call happened.** A test that only checks that a file store call happened cannot fail when a copy crosses into the wrong container; assert the full keyword arguments, and the type: `assert isinstance(sent, OrderCreateV2)`.
- **Give a stand-in the real shape.** Build it from the real class, so it refuses what the real one refuses. Build fixtures from the real object type: a driver's row object, not a dictionary.
- **When mocking an upstream error, copy the real service's wording verbatim**, and copy it again when the upstream rewords, in a commit that changes fixtures only. A paraphrase passes while production shows something else.
- **When behavior must hold for every route, variant or implementation, walk the table.** Write one contract test over the route table: every route passes the access policy on, every operation of a deprecated version is flagged, every member of a closed set has its variant. The next route is then covered the day it is added.
- **When two implementations must agree, run one set of scenarios against each**: a parity suite. For a port, the old code is the specification and the outputs match byte for byte. Check semantics that differ between engines against the real engine once, for example with string, null, missing, numeric, array and scalar values.
- **A regression test's docstring tells the failure story**, like this: "The batched loader's SQL has no ORDER BY, so rows arrive in arbitrary order. Truncating to `limit` before sorting picked an arbitrary status instead of the newest one."
- **When a reviewer or a review bot claims a test is missing, add it and cite a mutation check or the failure on the parent commit**: "Mutation check: adding a `break` after the logged close failure in `lifespan` makes the test fail". A proven gap is accepted, not argued.
- **Test what is usually assumed**: the second run of anything replayable; that a secret stays out of the log and that the line was written (`assert SECRET_VALUE not in caplog.text` and `assert "api_key" in caplog.text` in one test, or the first passes on an empty log); that the shipped configuration template loads; that a closed set is complete; that two values which must agree are one constant, asserted against the constant.
- **Work expected values out from the requirement**, as literals, never by calling the code under test.
- **When a test exercises the library and not the code, delete it**, because no change to this code can make it fail. Prefer no test over one that mostly tests mocks.
- **One function per scenario**; parametrization is rare. **Wait for a condition with a timeout, never for a fixed time**; patch clocks and sleeps.

Names and structure:

- A helper's name says what it builds: `minimal_valid_payload` is "The smallest message that conforms: every non-nullable column's source path is set".
- Structure a test so it reads without jumping to a fixture. Test setup stays inline per test, with no shared conftest state: plain helper functions imported across test files are fine where that is the file's established convention, and so is one automatic fixture that clears a class-level cache.
- Follow the repository's test convention and carry none into another repository. Where it pairs each test with a markdown specification, change both halves in one commit, because the prose can drift from the code. Where it does not pair, add neither the pairing nor the `# Given`, `# When`, `# Expect` comments.

A suite that does not run protects nothing.

Named practice behind these: classicist testing and "don't mock what you don't own"; consumer-driven contract testing (Pact) in spirit; mutation testing and "red before green", by hand; "DAMP, not DRY"; Behavior-Driven Development for the paired form.
