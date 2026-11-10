Test what was promised, not what was written, including what design-review.md changed, and then what the code reveals that no document mentions.

Two proofs are owed on every change, and neither replaces the other. Automated tests prove the logic. A run of the real thing in a real environment is the final gate: "The tests pass" does not answer "does it work". Defects live between the layers a test replaces. A stand-in that accepts any keyword argument hides a renamed client argument until creation fails in production, because the test never meets the real SDK's signature. A loader can pass its unit tests and fail its manual run on the real driver's `Row` object, a tuple subclass. Every feature change carries automated tests; a pull request without them has a gap, and you close it by writing them.

The rungs, highest first. For each outcome, say which one you reached:

1. The real product in its real environment, by the person or on a real device, from steps you write.
2. The built product in an environment made for testing: a development environment, a preview, a packaged application with a fresh data folder, a simulator.
3. The service or job started locally against a sandbox's real dependencies.
4. The real code with only its outermost input and output replaced.
5. One function called from a test.

Prefer a route that runs real code over one that replaces a layer. A lower rung is evidence for the logic only, and the report says so. Prove the whole path once end to end, then probe each edge at the cheapest layer that shows it.

Test on a best-effort basis. Coverage, whether to test and which route to take are yours, never asked.
