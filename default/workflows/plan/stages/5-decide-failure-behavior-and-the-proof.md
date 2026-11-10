Before planning tasks, fill one table for every interface and every step of the person's path:

| Case | Detected where | Result | Message or status | Pinned by test |
|---|---|---|---|---|
| Association name is whitespace only | Request model, at the API boundary | Rejected | 422, "association_name must not be blank" | `test_association_name_rejects_a_whitespace_only_value` |
| Upstream service returns 409 | Route, exception mapping | Passed through unchanged | 409 with the upstream detail | `test_attach_file_passes_an_upstream_409_through` |

Fill it by these rules:

- Every bad input is detected at the earliest layer that can see it, and its message names the rule and the value.
- An abnormal case raises. Nothing is tolerated, overwritten or truncated.
- A timeout or a wait that is part of normal operation is control flow, not an error.
- Name each survival boundary (a node, a background thread, a batch handler, the last-resort 500 path). Nothing else catches broadly.
- A partial failure reports each failed item and sets success to false.
- Configuration is validated at startup; unknown keys are rejected; a value that selects an environment is required with no default in code.
- Say what is logged and at which level. Identifiers only.

Then choose the proof for every row, and for every outcome in scope.md:

| What must hold | Proof |
|---|---|
| Pure logic | A unit test with literal expected values |
| A handler's behavior | The handler called directly, the outermost seam replaced, exact arguments and body class asserted |
| Something for every route, variant or adapter | One test that walks the table, or one parity suite run against each implementation |
| A write that can be replayed | A test of the second run |
| A sensitive value near a log | A test that searches the captured log |
| A defect being fixed | A regression test built from the real object type, failing on the parent commit |
| Something no test here can reach | A live verification step that says what to count, or an unchecked item for the person |

List the five inputs or failures most likely to hurt a person that the table does not cover yet: nothing, a very large value, a wrong type at a nested path, the upstream service down, the same request twice, an old stored record, a second user. Add a test for each to the task that owns the code.

Check the gate. Do the tests you are planning run in CI in this repository? If not, the plan says how they are run and reported, and adds the CI step when it is one job with no new secret.

Map every outcome in scope.md and every state in product-design.md to the test or the verification step that proves it. An outcome with no proof is a gap in the plan; close it before going on.

**Where the proof departs from common practice, and why**

- Tests do not gate every merge in every repository, although continuous integration practice expects them to. The written test plan with counts compensates in review, not in enforcement. So the plan says how each test is run and reported, and adds the gate when it is cheap.
- No coverage threshold gates a merge. Coverage is measured where the repository measures it; exact assertions, table-walking tests and mutation checks carry the weight. Do not plan a threshold unasked.
- Mutation checks are done by hand, and the contract between a consumer and a provider is pinned with verbatim error strings and parity suites, not with mutation-testing or contract-testing tools. The intent matches those practices without the tooling. Propose a tool only when the repository asks.
- A markdown specification paired with each test file goes beyond common practice and can drift from the test. Plan it only where the repository already uses it.
- Verification in a development environment is planned beside the unit tests, not in place of them: a hand-verified run with counts is the final gate.

### Questions to ask here

- Fail early and fail fast: where is the earliest place this can fail?
- Should this be allowed at all? Is it better to error out here, so that someone can look at what is happening?
- Is this timeout a bug or normal flow? Should the run fail only when there is a bug a developer must look at and fix?
- Can the success flag ever be false?
- Does this test gzip itself, or how we use gzip?
- Which change to the code makes this test fail?
- Does the mock accept anything? Then it proves nothing.
- Does this run in CI, or only on your machine?
- What does the user see when the upstream is down?
- Is the tag value in the log?
