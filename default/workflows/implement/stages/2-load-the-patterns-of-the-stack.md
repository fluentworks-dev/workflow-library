Find the sections below whose "Applies when" line matches the files you will touch, and use only those. They add the detail and the worked examples behind the rule lists in your instructions. The repository's own guidance and its neighbouring code win over all of them; where they differ and you followed the repository, say so under "Decided for you".

### In practice: Python services

Applies when the repository uses Python for a service or a library.

- Tooling: Pydantic v2, FastAPI; Poetry 2 or uv (uv when nothing is set up); ruff with double quotes and isort `known-first-party`; mypy inside the project environment; pytest in asyncio auto mode with `pytest-mock`.
- Pre-commit: a local hook that regenerates the committed API description, `check-yaml`, `end-of-file-fixer`, `trailing-whitespace`, ruff and ruff-format, and a local mypy hook over `src/`. CI runs `pre-commit run --all-files`. When ruff's defaults drift, pin the rule set in `ruff.toml` with a header that says why and which versions were verified. State pre-existing type errors with their count: "mypy passes (only pre-existing errors remain), 21 errors, all pre-existing".
- `str | None`, `list[dict[str, Any]]`; `str, Enum` with `__str__` returning the value; frozen dataclasses for value objects (`Principal`, `CallerIdentity`, `TableInfo`, `BatchCounts`); keyword-only signatures such as `def write_batch(..., *, idempotency_key_prefix: str)`.
- `collections.abc.Sequence` for read-only parameters; `from __future__ import annotations` where a heavy module such as pyspark must not be imported for typing.
- `ValueError` only for a bad argument, with the rule and the value in the message. Domain exceptions are raised `from error`. `assert isinstance(...)` narrows what an upstream layer guarantees.
- An abstract base class over a `Protocol` for a seam, because mypy verifies each override's signature against the base at class definition, and Python refuses to instantiate an adapter that is missing a method. Its docstring carries the error contract.
- `match` on typed operation classes in adapters: `case SetFieldPatch():`.
- `logger = logging.getLogger(__name__)`; `%s` arguments in logging calls and f-strings elsewhere; `exc_info=True` on the last-resort 500 path; an upstream 4xx at warning and a 5xx at error; `logger.exception` in jobs.
- Be honest about what does not exist: "There is no metrics system in this service today; when one lands, rejected-row reads are the right first counter." Do not build a metrics system for one counter.
- Never fetch a secret or build a client at import time. Build shared clients once at startup.
- A command-line tool converts small named exceptions at the command layer: `typer.echo(str(error)); raise typer.Exit(1)`. A script uses `argparse` with the full invocation in its module docstring.

A comment that explains both halves of a constraint:

```python
# min_length=1 surfaces the non-empty constraint in the OpenAPI schema; the
# validator additionally rejects whitespace-only values (which min_length alone
# would accept), keeping description parity with NameField.
DescriptionField = Annotated[
    str,
    Field(min_length=1),
    AfterValidator(description_validator),
]
```

Configuration that fails at startup, with the constraint in the message:

```python
        if len(hierarchy) != 1:
            raise ConfigurationError(
                "the document backend requires orders, line items, and shipments to map "
                "to one container: creating a line item or a shipment links it to "
                "its parent inside one transactional batch"
            )
```

A constant whose comment records the one exclusion and why:

```python
# Statuses whose body carries a user-actionable message from the upstream service
# (invalid link, missing target, referenced record, upstream validation,
# access denial). 401 is deliberately absent: it means our
# on-behalf-of token exchange failed, which is our problem, not the user's.
_PASSTHROUGH_STATUSES = {400, 403, 404, 409, 422}
```

What never to write: a router that matches exception text.

```python
    except Exception as e:
        error_msg = str(e).lower()
        if "not found" in error_msg:
            raise HTTPException(status_code=404, detail=str(e)) from e
        else:
            raise HTTPException(status_code=500, detail="Internal server error") from e
```

The replacement is a hierarchy in which each domain exception carries its own status, and a handler of four steps: try, call one method, re-raise a domain exception untouched, and for anything else log with the traceback and raise the internal error.

Tests:

- Call route functions directly as coroutines, with a `SimpleNamespace` service whose API objects are `AsyncMock`s. Assert the class of the body that is sent, `assert isinstance(sent, OrderCreateV2)`, so a renamed keyword argument cannot break creation in production unseen.
- No `conftest.py` state: plain helper functions imported across files and named for what they build (`_make_order_doc`; `minimal_valid_payload`, "The smallest message that conforms: every non-nullable column's source path is set"). One function per scenario; parametrization is rare.
- An `AsyncMock` accepts any keyword argument. Bind it to the real signature (`create_autospec`, `spec_set`) or assert the full keyword arguments: a manager test that only checks that a file store call happened still passes when a copy crosses into the wrong logical container, and one that asserts the full keyword arguments fails.
- `TestClient` only for middleware and exception handlers, with throwaway routes under `/_test/...` on the real application. `caplog` for log hygiene, `tmp_path` for real files, `CliRunner` for a command-line tool. One autouse fixture that resets a class-level cache is acceptable.
- Where the repository pairs each test file with a markdown specification, follow it to the letter: a folder per source file holding `test_<name>.md` and `test_<name>.py`; one markdown section per test function, named identically; prose "When ... Expect ..." with no bullet lists; the three comments. Change both files in the same commit, because the prose and the test can drift apart. Do not introduce the pairing where it is not used.

```markdown
## effective_value with line-item-level document returns line item value when set

When `effective_value` is called on a document with line-item-level tags `{priority: "low"}` and account-level tags `{priority: "high"}`, looking for key `priority` in the line item entity level shape `(tags, order_tags, account_tags)`.

Expect the returned value is `"low"`, showing that nearest-level override beats the account-level value.
```

```python
def test_effective_value_with_line_item_level_document_returns_line_item_value_when_set():
    # Given
    doc = {"tags": {"priority": "low"}, "order_tags": None,
           "account_tags": {"priority": "high"}}
    levels = ENTITY_TAG_FIELDS["line_item"]

    # When
    result = effective_value(doc, "priority", levels)

    # Expect
    assert result == "low"
```

### In practice: TypeScript and React

Applies when the repository uses TypeScript, with or without React.

- zod and `safeParse` at the boundary, `z.infer` for the type; string enumerations with PascalCase keys; `interface XProps` for component properties.
- `null` for absent at a boundary and `throw` for a programmer or configuration error. `UploadTarget = "azure" | "s3"`, `readonly` class fields, `as const` tuples, no `any` in new modules.
- An unknown value from another system is rendered raw, not hidden. A blank optional field is left out of the payload, never sent as null or empty, so the upstream default applies.
- Small custom hooks that pair state with a ref, so event handlers compare against the latest value. A docstring can be the bug report: the hook that keeps a server error visible while a person edits a form carries, as its docstring, the failure it fixes.
- The naming rule holds in quickly built applications too: an application written in a few days still gets full names, never one-letter ones.
- A helper typed against the least it needs: `headers: { get: (name: string) => string | null }`, not `Headers`.
- A vocabulary module's comment names its source: "The source of truth is the upstream service's `FileAssociationName` enum ... this list is a set of suggestions, not a constraint".
- A module shared between bundles carries its reason in the header ("Kept dependency-free so the proxy (middleware bundle) and the standalone presence server can share it"). A constant duplicated because of a bundle constraint carries "Must match <path>".
- Formatting goes in separate `style:` commits. The gate is Biome or `tsc --noEmit`.
- Tests: intercept with `page.route` and capture the posted JSON; add an `aria-label` to every new control; Node-side tests for pure modules under the same runner, "so one runner and one coverage pipeline serve both"; behavior names such as `refuses a value too large to be read back rather than truncating it` and `keeps every cookie under the browser limit for a claims-heavy session`; helpers named for what they build (`sessionExpiringIn`, `keepaliveRequestFor`). When the runner is starved, reduce the workers and collect coverage on one browser.

A refusal that names the failure it prevents and carries the numbers:

```ts
  // joinSessionChunks only reads MAX_SESSION_CHUNKS cookies back, so writing more would
  // hand the browser a session that can never be reassembled: the sealed value comes back
  // truncated, fails to decrypt, and the presence check in the proxy still passes because
  // session.0 exists. That reads as an endless login redirect with nothing in the logs.
  // Refuse here instead, where the callback's catch reports the reason.
  if (chunks.length > MAX_SESSION_CHUNKS) {
    throw new Error(
      `Sealed session needs ${chunks.length} cookies but only ${MAX_SESSION_CHUNKS} are read ` +
        `back (${sealed.length} bytes at ${SESSION_CHUNK_SIZE_BYTES} bytes per cookie). Raise ` +
        `MAX_SESSION_CHUNKS or store less in sealSession.`
    );
  }
```

### In practice: Terraform and Terragrunt

Applies when the repository uses Terraform or Terragrunt.

- A file header is a prose paragraph on the resource's role and its rollout contract. Every output has a description. The primary resource is `this`.
- A variable exists only when environments differ. An escape-hatch input defaults to empty so existing plans stay clean. An input nothing consumes is removed ("component_slug was declared without being consumed").
- "a unit knows itself and its parents, never its children"; a unit that forgets an input "fails loudly on `terraform validate` rather than silently mistagging". This departs on purpose from removing repetition through includes.
- A deliberate value is labeled: "Explicit rather than the provider default, so a reader sees it is a choice". A size carries its price ("about USD 900 per month for the brokers"), and a sandbox takes the smallest size because "sandbox data is disposable".
- A module that may run in more than one cloud partition (a commercial and a government partition, for example) composes resource names from the partition and holds no region literal.
- Every GUID and account identifier has a trailing comment, because "a reader checking whether a value is the staging or the production registration should not have to look it up".
- `terraform fmt -check` and `terragrunt hcl format --check`.
- Apply and destroy are exact steps for the person. An input is never loosened to make a destroy pass.
- Tests: `command = apply` against the mocked provider, "so the provider's own name validation runs as well"; `terragrunt hcl validate --inputs --strict`, "which `terraform validate` never checks". Names are pinned because "Renaming an applied resource is not a free convention cleanup". After an import the plan "must report no changes".

Two settings that read the same and mean different things, with the reason at the point of temptation:

```hcl
    # The literal repo name here is deliberately NOT local.repo_slug, even though
    # the two read the same today. This prefix is the storage path of state that
    # already exists; repo_slug is a tag value and the apply role's ABAC scope.
    # Wiring them together would mean that editing repo_slug silently relocates
    # every unit's state, and each one would then plan its resources as new.
    key          = "platform-modules/${get_path_from_repo_root()}/terraform.tfstate"
    region       = "us-west-2" # where the state bucket lives, not where workloads deploy
```

### In practice: Spark data pipelines

Applies when the repository uses Spark, Delta tables or Databricks notebooks.

- A notebook is a `.py` file with the `# Databricks notebook source` header: a long linear script with small named predicate functions at the top, numbered stage headers, and data-flow cells free of inline rules.
- Widgets are read through one helper and validated at the top: `"trigger_mode must be one of {TRIGGER_MODES}, got {trigger_mode!r}"`. Predicate functions are annotated even when the rest of the notebook is not.
- Logic duplicated between notebooks that cannot import each other carries the "keep the two copies in step" comment in both.
- `display()` each stage, sorted so the interesting rows surface first. Place `cache()` before the display that populates it, and unpersist it. `print()` batch counts and elapsed seconds.
- Prefer the call that stays inside the engine, and say why: "DataFrame.isEmpty runs in the JVM; RDD.isEmpty would pull a row over a Python socket". Prefer a deterministic aggregate (`max`) to an order-dependent one (`first`), with a comment on why.
- A broad `except` in a batch handler is a survival boundary: "avoid breaking pipeline at all costs".
- Write a known limitation where it bites: "Known limitation: inference merges a struct with a scalar into STRING ... every row of the batch is rejected. Nothing is lost: rejected rows keep their exact Kafka bytes."
- A one-off backfill notebook ships with the forward change and is deleted once it has run.
- Tests: a local Spark session in the pipeline's test folder; when porting, parity byte for byte for every entity, with the old code as the specification; when a notebook cannot be imported, its functions extracted from the source with `ast` and executed into a namespace; small real Delta tables in a temporary path; `test_replayed_batch_id_writes_nothing_twice`; a regression test for every production surprise, pinned the first time it happens (a bare null that gives a column no type, for example). Where CI only validates the bundle, run the tests by hand and paste the counts.

A rule as a named function whose docstring is its version history:

```python
def effective_is_refunded() -> Column:
    """Refunded state for a payment event, with a version-gated legacy fallback.

    From producer version 9.0.0 status is a uint32 bitfield (not the old enum) and
    legacy_status is no longer emitted, so is_refunded is authoritative there and
    null means the refund state is genuinely unknown. Below 9 (or when the version is
    missing, which only happens on old rows), rows that leave is_refunded null encode it
    in status: when legacy_status is true, status == 4 (REFUNDED) means refunded
    and anything else (including a null status) means not refunded.
    """
```

### In practice: ROS 2 and robots

Applies when the repository uses ROS 2 nodes in Python or C++, or runs on equipment a person operates.

- C++ work is lifecycle flags, lifecycle events and renames. Adopt the surrounding file's indentation and `RCLCPP_*` style, and trailing-underscore members.
- A guard comment where another service depends on a file name: `// IF YOU CHANGE THIS FILE NAME, MAKE SURE TO UPDATE THE CONSUMING SERVICE`.
- Catch at the node boundary: "Must not raise, failures are caught & logged so the node stays alive." The reasoned exception: "a missing run record ... would silently detach this run from its launch, so an upload failure must fail configure".
- Parse the run configuration up front, so a malformed file fails fast at configure time instead of surfacing inside the background upload thread.
- The low-level writer raises `ValueError` on a bad argument. The node validates, warns through the ROS logger, "so the warning lands in the ROS log with consistent formatting", and falls back to the default.
- A partial failure is listed per file, with `success = not failed_files`.
- Units: `FORCE_THRESHOLD_NEWTONS`, `travel_distance  # mm`, and megabytes as `* 1000 * 1000` with the comment `# MB (megabytes): 1 MB = 1000 * 1000 bytes.`
- Operator options are kept: more options give the operator more ways out when things go wrong. A default shared by a node, a launch file and a template is defined once.
- When a robotics engineer cites the platform's design guideline, follow it. Move a subscription or a thread only with measured evidence, and fix the consumer first.
- Follow the repository's commit convention and its pull request template, "so that the robotics team can correctly make release notes".
- Tests: stub `rclpy` and the interface packages in `sys.modules` before import, or load the module by file path to bypass a package `__init__`. Construct nodes with `__new__`, or call unbound methods on a `SimpleNamespace` self. Keep the ROS stubs in one shared test module, not copied into each test file. A `SimpleNamespace` self that mirrors private attributes is brittle; extract the logic into a pure function where you can. CI only builds, so complete the template's test plan and leave hardware items unchecked (a test on the real machine in its offline mode, for example). The reviewers decide what counts as tested on a machine (approved pending a test on the simulator, for example).

A callback that feeds a queue never blocks. The mechanism, as a first draft might have it:

```python
error_message: str | None = None
try:
    self._queue.put(msg, block=False)
except queue.Full:
    error_message = f"queue is full (maxsize={self._queue.maxsize}); messages are being dropped"
except Exception as e:
    error_message = f"unexpected error: {e}"

if error_message:
    now = time.monotonic()
    if now - self._last_push_error_log_time >= 10.0:  # seconds
        self.get_logger().error(f"Failed to push to queue: {error_message}")
        self._last_push_error_log_time = now
```

Keep the mechanism (a non-blocking put, a drop on full, one line per interval). On a new line write it to the current bar: `error`, not `e`; the interval as `_PUSH_ERROR_LOG_INTERVAL_SECONDS`; the last-logged time initialized to `None`, never `0.0`, so the first fault always logs.

### In practice: GraphQL

Applies when the repository uses GraphQL as a read layer over other stores.

- Batching comes from the loader's base class; do not write it per loader. Sort key lists before rendering SQL, so the rendered string is a stable cache key.
- Validate field paths against a pattern. Do not copy a layer that only quotes caller-supplied filter values.
- Never fetch a secret at module import, or the unit tests need cloud credentials; fetch at startup.
- Tasks follow the loader's natural order: the skeleton with the empty-keys guard, then the SQL, then the resolver wiring.
- Where CI runs lint only, run the unit tests by hand and record the counts. Add the test job once the tests need no credentials.
- Tests: let the batching library and the cache run for real. A regression test builds its fixture from the row type the database driver returns (a tuple subclass, not a dictionary) and quotes the failure: "The batched loader's SQL has no ORDER BY, so rows arrive in arbitrary order. Truncating to `limit` before sorting picked an arbitrary status instead of the newest one."
- Close with a manual run against a development environment: compare query history and response shapes, measure before and after, cold and warm, in seconds, and state the limit of the fix (for example, that it only stops the bleeding).

### In practice: Bash

Applies when the repository uses shell scripts for deployment or operations.

- List the prerequisites in a header comment. A missing variable stops the script with "Missing required environment variables"; it never produces an empty value.
- A new required variable is added in every place it must exist (the template, the allowlist, the workflow's environment, the environment's settings, the documentation table) in the same change.
- Make the script idempotent and safe to run twice, and give it a dry run when it changes anything.
- On bash older than 4.4, an empty array under `set -u` is an error; expand it as `${flags[@]+"${flags[@]}"}`.

A skeleton that follows the rules (illustrative, not taken from a repository):

```bash
#!/usr/bin/env bash
# Renders the service template and prints the deployment it would make.
# Prerequisites: envsubst; the variables listed in REQUIRED_VARS.
set -euo pipefail

environment_name="${1:?Usage: render.sh <environment-name> [slot-name]}"
slot_name="${2:-}"

REQUIRED_VARS=(REGISTRY_NAME APP_NAME IMAGE_TAG)
for variable_name in "${REQUIRED_VARS[@]}"; do
  if [[ -z "${!variable_name:-}" ]]; then
    echo "Missing required environment variables: ${variable_name}" >&2
    exit 1
  fi
done

rendered_template="$(mktemp)"
trap 'rm -f "${rendered_template}"' EXIT
# Allowlist: only these names are substituted, so any other $ in the template survives.
envsubst '${REGISTRY_NAME} ${APP_NAME} ${IMAGE_TAG}' < template.yaml > "${rendered_template}"

slot_arguments=()
if [[ -n "${slot_name}" ]]; then
  slot_arguments=(--slot "${slot_name}")
fi

echo "Environment: ${environment_name}"
echo "Image:       ${REGISTRY_NAME}/${APP_NAME}:${IMAGE_TAG}"
echo "Slot:        ${slot_name:-none}"
```

### Questions to ask here

- Which neighbouring file did you copy the shape from?
- Is this how this repository does it, or how you would do it?
- Does this work in every cloud partition it must run in?
- What unit is this number?
- Why a new helper? Is there an existing model, such as a `Point3` type, to reuse?
