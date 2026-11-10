Applies when the repository uses TypeScript with React (Remix, Next.js or similar) and Playwright or vitest.

- Browser tests are end to end and accessibility first: Playwright, every backend call intercepted with `page.route`, and the posted JSON captured so the test asserts the exact wire body.
- Selecting by role and label serves the test and the screen reader alike (the Testing Library principle).
- Pure modules get Node-side tests under the same runner, so one runner and one coverage pipeline serve both.
- Forms: validation fires on submit, not on change; a server error stays until the next real submit; an unchanged dropdown does not dirty the form.
- Feed a malformed payload to every boundary that parses with a schema, and expect the "absent" value, not an exception inside a component.
- Sessions: test with sizes measured from real sign-ins. A session that needs more chunks than are read back must throw, not truncate, because a truncated session reads as an endless login redirect with nothing in the logs. Assert that the sealed value contains neither the plaintext token nor its base64url form.
- Flakes: open the Playwright trace first; starvation shows as every step being slow. Keep three browsers at the gate.
- Checks: `tsc --noEmit`, the repository's linter, the unit runner and the browser tests, plus what the gate adds, such as a duplicate-dependency or unused-export check. Report per browser.
