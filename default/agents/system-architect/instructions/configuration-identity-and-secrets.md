## Configuration

- **One settings object**, read from the environment and validated at startup (twelve-factor), never mid-run, with a message that explains the constraint. Unknown keys are rejected: typos must surface rather than being silently ignored.
- **When a value selects an environment** (a URL, a client identifier, a tenant), make it required with no default in code, and ship the staging value in the template with a test that the template is self-consistent. **When it only tunes behavior**, make it optional with a documented default. A wrong default silently points a deployment at the wrong environment.
- **When a service defaults to the sandbox development environment** so a fresh clone runs, production overrides every secret that has a development default with a self-describing placeholder (`ignored-placeholder-to-prevent-default-value`), and a target that is not ready fails validation on purpose.
- **When a backend or mode is selected by configuration**, use a closed selector so a typo fails at settings load.
- **When a feature flag and a runtime input mean different things**, keep both and explain the difference: with no job reference the file is still uploaded but not linked to a job; with the flag off nothing is uploaded at all.
- **When a value is the same in every environment**, hard-code it and list it as hard-coded. **When it differs**, it is configuration with the same name everywhere: never put values that differ per environment at repository scope, or the reverse.
- **When a value must reach a browser bundle**, declare it as a build-time argument with a comment saying why a run-time setting would not reach it. Secrets go through secret mounts, never build arguments.
- **When adding a required variable**, touch every place it lives in one pull request (template, script allowlist, workflow environment block, hosted environment, documentation table), and ask whether the infrastructure code needs the new variable too.
- **When a path or parameter would be hard-coded for convenience**, prefer configuration. The common drift is tenant identifiers, hosts and one individual's email as an alert address written as literals; an alert address is a group address set per environment.
- **When an opaque identifier appears in configuration**, name it in a trailing comment.

## Identity and secrets

- **The server decides identity**: one component decides identity, the server. No fallback chain, no comparison of strings from two sources.
- **Tokens never reach the browser.** Measure the limit, then design for it: a session that comes close to the 4096-byte cookie limit is chunked with a hard bound that throws rather than truncating it.
- **When a platform can prove identity without a secret**, use it, detected by the variable it injects, and keep the secret path for laptops. One registration per environment, so a staging credential can never grant access to production and there is no client secret to rotate.
- **No service-credential fallback for a user's action. Delete an insecure path; never leave it dormant. Fail closed.**
- **When a sensitive value could reach a log**, log identifiers only and write a test that searches the captured log: tag values are never logged; keys are fine.
- **Compliance drives topology.** Name the boundary each component sits inside and check that every service it adopts is available there, for example in a government cloud region.
- **Least privilege in CI**: a narrow role per principal; a build of a pull request never pushes; trust roots, first applies and destroys stay with a person.
- **When a secret is managed outside the infrastructure tool**, keep a placeholder, list it among ignored changes (`ignore_changes`) and write what an unguarded apply would do (for example, lock out every user). **When the tool must create one**, generate it in the run through a write-only argument with a rotation counter, so it never enters plan output or state.
- **A security finding the design does not close** blocks, or is a row in Decisions, decided by the person, with the condition that closes it.

## In practice: Python services

Applies when the repository uses Pydantic settings.

- One `Settings(BaseSettings)`; selectors typed `Literal["cosmos", "mongodb"]`; a table of required settings per backend checked by the store factory at startup.
