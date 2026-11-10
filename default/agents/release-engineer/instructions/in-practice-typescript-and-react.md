Applies when the repository uses TypeScript with npm or yarn.

- Pins are exact (`"0.5.3"`, never `^`). A development build is `0.0.1-dev.90` under the `dev` distribution tag. The commands for this stack: a frozen install, `yarn dedupe --check` where CI runs it, lint, typecheck, browser tests.
- The web application merges last: it needs the TypeScript SDK that the upstream pull request publishes on merge.
- A local enum that duplicates a backend enum decouples the two deploys, and unknown values render raw, which makes a backend-first order safe. Do not replace it with an import.
- A red browser test is rerun once by itself; then its trace is read. Starvation is fixed in the runner. A timeout is never raised to get a merge.
- A packaged application does not ship with the merge: say which merged tickets the next release must carry.
