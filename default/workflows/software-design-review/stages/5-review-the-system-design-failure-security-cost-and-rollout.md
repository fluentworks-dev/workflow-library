**Failure and concurrency.**

- For each write: what happens if it runs twice, if it stops halfway, and if two actors do it at once. Look for a repeatable write, a version check on a write that depends on a read, and a written order for two writes that cannot be atomic, chosen so the next run repairs a failure between them.
- Nothing is lost: what cannot be processed is kept as received, with the reason. Every queue and buffer is bounded and says what happens when it is full.
- Work that can outlast a caller returns at once and exposes a state to poll.
- Bad configuration fails at startup. A process that must survive catches at its boundary and reports. A wait or a timeout that is normal is not an error: a workflow fails only when there is a bug a developer should go and fix. An abnormal case raises instead of overwriting, truncating or returning a placeholder.
- Cleanup happens once, in one place. A lock wraps only the mutation.
- A partial failure names every failed item and reports failure. A retry wraps only what is safe to repeat, is bounded and is logged.

**Security.** Mandatory, whether or not the design has a security section. Walk STRIDE over each new boundary and data flow and check:

- Who can call each route, job and tool, and where that is enforced.
- One authority decides identity, on the server. No credential reaches the client. Identity the platform proves is used where it exists. Least privilege per actor. Missing identity fails closed.
- Every value that reaches a query is bound as a parameter; quoting is not enough.
- Logs carry identifiers, never secrets, tokens or personal data.
- No credential is reachable from code that a pull request controls.
- No default can make a deployed environment read a development value, and no tenant, account or personal address is hard-coded.
- An insecure path that is no longer used is deleted, not left dormant.

Record every finding with its answer: a fix, or a reason you verified.

**Cost.** List what is paid for each time it runs and what decides how often it runs, and compare each with the least the feature needs. Flag work that grows with history and not with what is in use: reads without a limit, a call per item in a loop, polling that never goes quiet. Each size is the smallest that meets the requirement, with its price and the price's date, and the estimate covers one user and a hundred organizations. A sandbox gets the smallest and the shortest.

**Performance.** Every freshness, limit, size, retention and timeout has a number, a unit and a reason; a number chosen by feel says so. A claim about speed has a plan to measure before and after and says what the fix does not solve. A mitigation that only hides a cost (a throttle, a longer timeout) is weighed against removing the cost.

**Operation.** What is logged, what is counted, what alerts whom, and how a failure would be diagnosed. The design says what does not exist yet instead of implying it does.

**Rollout.**

- The order keeps the running system serving before, during and after: expand, then the code, then the migration, then the removals as a later change. A migration is applied before the code that reads it serves a request. A drop or a rename of anything the live system still reads never ships with the code that stops reading it.
- Each step names who or what runs it, that it can be run twice, how it is checked and what undoing it does. An undo that is not the mirror of the step says so: for example, a rollback after cutover silently loses data.
- Several repositories: the merge order, and each consumer's move from a development build to the released one as a named last step.
- What only a person can do (an apply, a destroy, a secret, a release, a check on a device) is written as exact steps, each with the condition that proves it worked.

**Proof.** Every outcome has a named proof at a named level that runs where merges are gated. Tests prove the logic; a run against a real environment is still the final gate, so the design names that run and what it must show.

**When the design touches a machine or the edge, also check** it against the domain's published lifecycle guideline before anything else; a timer instead of a thread for periodic work; the deactivate timeout sized with the consumer's read timeout; queues drained on deactivate; retries that a deactivate can interrupt; a test on a real or simulated machine written as a step for the person, with unverified items left unchecked; and breaking changes for operators given with the exact configuration text.

**When the design touches infrastructure as code, also check** that names of applied resources are final here and pinned by an offline test before the first apply; that a rename uses the tool's own move and import mechanisms and its plan shows zero to destroy; that nothing live is pinned to an unmerged branch; that a plan job running a pull request's code holds no administrator credential; that expected plan counts are the acceptance condition (12 to add, 3 to change, 0 to destroy); that environment codes come from names already applied; and that the README and the guidance file change in the same commit as a convention.

**When the design touches CI/CD, also check** that tests the design adds run on every pull request; that a new environment is a thin trigger and not a copy of the job; that a disabled safeguard has its constraint written beside it; and that a dead workflow is deleted in its own commit, naming what still references it. When the design ships a platform, it runs the platform through its own pipeline and pushes canary applications through it before announcing: one hard, one trivial.

**When the design adds a script, also check** a dry run, a second run that changes nothing, a bound on concurrency, logs and state written outside the repository or under an ignored path, and the same bar as the service: bound parameters, no hard-coded path or tenant, named exceptions.

**Questions to ask here**

- Is this the smallest size that meets the need? A sandbox should take the smallest.
- Does this work in every cloud partition and region it must run in?
- What happens if this runs twice? If it dies between the two writes?
- Who can call this, and what if they lie?
- Should this raise instead of overwriting?
- Is this a timeout a developer has to fix, or just a wait?
- Does a new variable have to be added to the infrastructure code as well?
- Who applies this, and what proves it worked?
- Who gets told when this fails at night?
