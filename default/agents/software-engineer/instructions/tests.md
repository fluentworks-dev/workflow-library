- When writing a test, write it first and see it fail because the behavior is missing. Name the code change that would break it. Expected values are literals worked out from the requirement.
- When naming a test, write a behavior sentence (`test_no_empty_trailing_file_when_export_ends_at_size_limit`), and structure it so it reads without a fixture. A repository convention of all setup inline per test and no shared fixtures means no hidden shared state; builder helpers are fine where they are the established convention of the file. Follow the repository's test convention exactly, including a paired markdown specification with Given, When, Expect where it has one.
- When mocking, patch only the outermost input and output call and let libraries, caches and concurrency run for real. Assert the exact arguments and the exact body class: a mock that accepts any keyword arguments never catches a mismatch with the real SDK's signature.
- When a stand-in replaces an outside system, give it the real shape, so it refuses what the real one refuses. Prefer no new test over one that mostly tests mocks.
- When behavior must hold for every route, variant or adapter, write one test that walks the table (every route threads the access policy; every enumeration member has a union variant), or a parity suite run over the adapters. When porting, the old code is the specification.
- When a write can be replayed, test the second run (`test_replayed_batch_id_writes_nothing_twice`).
- When a reviewer or an automated reviewer says a test is missing, add it and cite a mutation check or a failure on the parent commit in the reply.
- When manual verification finds a bug, add a regression test built from the real object type, with the failure story in its docstring.
- When mocking an upstream error, copy the real service's wording verbatim, and copy it again when the upstream rewords, as a fixtures-only commit.
- When a test exercises the library and not the code, delete it: a compression test that only checks the compression library tests the library, not how the code uses it.
- When a change touches a legacy suite that no longer runs, delete the suite once its scenarios are covered elsewhere, and say so.
- When an existing test breaks, assume the change is wrong. Edit the test only when the design changes that behavior, and say so under "Behavior change in tests:" in the commit body. Otherwise stop and report it rather than editing it.
- When tests cannot cover something, write it as a live row of the test plan with what to call, compare and count; Validation runs it and records the counts (rows compared, rows matching).
- When relying on a test, check that CI runs it. Where it does not, say so in the test plan, where Validation runs it by hand with counts, and add the CI job when it needs no new secret. Never fetch a secret at module import: it makes unit tests need credentials.
- When a test is flaky, read its traces before touching the code. Split a long test; do not raise its timeout.
- When waiting in a test, wait for a condition with a timeout, never for a fixed time.

This is classicist testing ("don't mock what you don't own"), DAMP over DRY, and mutation testing done by hand. Two departures from common practice: tests do not gate every merge here, and no coverage threshold is enforced.
