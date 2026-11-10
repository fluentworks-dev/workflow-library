The Software Implementation workflow carries the worked examples for each stack.

### In practice: Python services

Applies when the repository uses Python with FastAPI, Pydantic or pytest.

- `async` throughout; ruff (88 columns, rules `E,W,F,I,B,C4,UP`); mypy as a local hook over `src/`; Poetry or uv, whichever is there.
- `str | None` syntax, `Annotated` constraints, `Literal` selectors, `str, Enum`. Frozen dataclasses for value objects and Pydantic only at boundaries. Abstract base classes over `Protocol` for a seam with adapters, with the error contract in the base's docstring. Keyword-only parameters after `*`.
- `%s` arguments in logging calls; every `# type: ignore` and `# noqa` with its code; no secret fetched and no client built at import time.
- Tests: call route functions directly with a `SimpleNamespace` service of `AsyncMock`s, and use the full client only for cross-cutting behavior. `assert isinstance(sent, <BodyClass>)`. No shared `conftest.py` state. Where test files are paired with a markdown specification, change both in one commit.

### In practice: TypeScript and React

Applies when the repository uses TypeScript.

- Parse at every untrusted boundary with the schema library and infer the type from the schema. `null` for absent, `throw` for programmer errors. Narrow string unions, `readonly`, `as const`, no `any`.
- Pure vocabulary modules with a source-of-truth comment and a Node-side test. A helper typed against the least it needs. A value too large is refused with the numbers in the message, never truncated.
- Tests: end to end where the repository does, with every backend call intercepted and the posted body asserted. Select by role and label, so every control gets an accessible name. The naming rule holds in quickly built applications too.

### In practice: Terraform and Terragrunt

Applies when the repository uses Terraform or Terragrunt.

- Every variable described and validated with a message that says why. Locals over variables: no variable added just in case. One `local.names` map. Every unit writes out every input. Every GUID named in a comment. A size carries its price, and a sandbox takes the smallest.
- Two settings that read the same and mean different things stay separate, with the reason in a comment. No region literal in a module that may run in another partition.
- Tests: offline `terraform test` with a mocked provider, `expect_failures` for validations, every name pinned. Validation records the plan counts in the test plan (to add, to change, to destroy). Never apply.

### In practice: Spark data pipelines

Applies when the repository uses Spark, Delta tables or Databricks notebooks.

- `.py` notebooks with widgets validated at the top, before any Spark work. A validity rule is a named function returning a `Column`, with its history in the docstring. Thresholds are unit-named constants at the top.
- A failing batch goes whole to the exceptions table with its raw bytes. Log a shape failure once per batch. `cache()` is placed before the display that fills it, and unpersisted.
- Tests: a local Spark session, replay tests, byte-for-byte parity when porting, and `ast` extraction when a notebook cannot be imported. Where CI only validates the bundle, the test plan says so, and Validation runs them by hand with counts.

### In practice: ROS 2 and robots

Applies when the repository uses ROS 2 or runs on operated equipment.

- The node must not die and the cloud must not block a run. Parse configuration at configure time. The writer raises; the node validates, warns through the ROS logger and falls back.
- A callback that feeds a queue never blocks: a bounded queue, a drop on full, one log line per interval.
- C++ is plumbing, in the surrounding file's style. Defer to the domain experts on lifecycle and threading. Follow the repository's own commit convention and pull request template.
- Tests: stub the ROS modules in `sys.modules` or load by path, construct nodes without `__init__`, patch sleeps and clocks, and skip at module level when real interfaces are required. A hardware check stays unchecked until someone runs it.

### In practice: GraphQL

Applies when the repository uses GraphQL as a read layer with batched loaders.

- A loader implements only the query and the primary-key filter. Bind filter values as parameters; never interpolate them.
- Tests: patch the one function that executes the query and prove coalescing: two concurrent lookups, one query.

### In practice: Bash

Applies when the repository uses shell scripts.

- `set -euo pipefail`; `${1:?Usage: ...}`; `mktemp` with a `trap` cleanup; `envsubst` with an allowlist after a `REQUIRED_VARS` loop; optional flags built as arrays; the configuration printed before acting.
