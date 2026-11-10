The plan is written for a builder with none of your context: a later session, or another agent. The builder reads the plan, not your memory, so everything it must obey is in it.

Break the work into tasks by logical grouping, dependency and amount of work. Order them foundation first: types and pure logic, then the layer that owns the rules, then the surfaces, then the wiring. A seam goes in with "no behavior change" before the new behavior uses it. A guidance file or README change goes in the task whose code makes it true. Each task is one test-driven loop (red, then green), leaves its own test green and is one commit, with its subject written now.

Write the plan in this order:

1. **Head**: the goal in one sentence; the architecture in two or three; the technologies; the reference files to read before building, each with why; and **Waits for**: each change from another ticket that must be on the default branch before the first task, with its ticket, its pull request and what this build uses from it, or "nothing". A task never builds on another ticket's unmerged branch.
2. **Global constraints**, from your grounding notes, quoted from the repository where it states them:
   - the branch name (a slug of three or four words naming the scope), and the shape of commit subjects and of the pull request title in this repository;
   - where tests go and how they are written, for example "New tests go in `agent_tests/` mirroring `src/`, never in `tests/` ... paired `test_<name>.md` + `test_<name>.py` ... all setup inlined per test, no shared fixtures";
   - "Do not modify existing tests ... if an existing test breaks, stop and report it rather than editing it."
   - the checks that gate the pull request, for example "(ruff, ruff-format, mypy on `src/`, and the OpenAPI generator) must pass": CI runs them on every push and Software Validation runs them once with counts; a task runs only its own test;
   - the style rules: "no single-character identifiers (except `_`), no abbreviations in new code, unit suffixes on numeric constants. Keep diffs small; do not rename untouched lines."; no em dashes;
   - the co-author trailer every commit message must end with, naming the model, when the repository or the session requires one.

   Reason: rules that live only in a repository are applied unevenly across repositories; the plan reaches every repository.
3. **File map**: Action (Create, Modify, Delete), Path, Responsibility. One row per file.
4. **Review focus**: the edge cases, each pinned to the test that covers it.
5. **Tasks**, each in this template, followed by the real code for its core (the test, the signature, the data structure, the key logic):

```
## Task N: <scope>
**Files:** Create `<path>` / Modify `<path>` / Delete `<path>` / Test `<path>`
**What it delivers:** <the behavior that exists after this commit>
**Interfaces:** Consumes `<exact name and type, from an earlier task or existing code>` / Produces `<exact name and type later tasks rely on>`
**Decisions:** <each choice made in this task and the alternative it beat>
- [ ] Write the test `<name, as a behavior sentence>`
- [ ] Run `<command>` and read the expected failure: `<the failure>`
- [ ] Implement
- [ ] Run it green
- [ ] Commit: "<subject, written before the code exists>"
```

6. **Verification**, the closing task, which Software Validation runs: the exact commands for the whole change, then a run against a local stack or a development environment for the rows the proof table of stage 5 marks as reachable by no test, each with what to call, what to compare and what to count (query history, response shapes, row counts). When every row has a test, the closing task is the commands alone, and says so.
7. **Out of scope (do not implement)**, enumerated, not implied.
8. **Self-review notes**: what you found and resolved while planning.

A filled plan, abridged to its head and two tasks:

```
# Association name on file attach: implementation plan
**Goal:** A person can name what an attached file is for, and a blank name leaves the upstream default in place.
**Architecture:** The backend adds one optional field to the attach request, validates it with one annotated type at the API boundary, and forwards it unchanged. It stores nothing and has no default of its own.
**Tech stack:** FastAPI, Pydantic v2, the generated upstream client, pytest.
**Reference files (read before implementing):** `src/models/requests/validated_fields.py` (how `NameField` is built); `src/routers/v2/files.py` (the attach route); `tests/routers/v2/test_files.py` (how routes are tested here).
**Waits for:** nothing. The asset service already accepts the name on its released version.

## Global Constraints
- Branch `asset-association-name`. Commit subjects are capitalized sentences naming the effect.
- Tests go in `tests/`, mirroring `src/`. Route functions are called directly with a `SimpleNamespace` service. No `conftest.py`.
- Do not modify existing tests; if one breaks, stop and report it.
- `pre-commit run --all-files` (ruff, ruff-format, mypy on `src/`, the OpenAPI generator) gates the pull request; CI runs it on each push, Validation runs it once with counts.
- No single-character identifiers except `_`, no abbreviations in new code, unit suffixes on numeric constants, no renames of untouched lines, no em dashes.

## File Map
| Action | Path | Responsibility |
|---|---|---|
| Modify | `src/models/requests/validated_fields.py` | Adds `AssociationNameField` |
| Modify | `src/models/requests/v2/files.py` | Adds the optional field to `AttachFileRequestV2` |
| Modify | `src/routers/v2/files.py` | Forwards the name; omits a blank one |
| Modify | `openapi.json` | Regenerated by the hook, never by hand |

## Review Focus
- A whitespace-only name is rejected, not trimmed to empty and forwarded: `test_association_name_rejects_a_whitespace_only_value`.
- A blank name sends no key at all, not null: `test_attach_file_omits_a_blank_association_name`.

## Task 2: Validate the association name at the API boundary
**Files:** Modify `src/models/requests/validated_fields.py` / Modify `src/models/requests/v2/files.py` / Test `tests/models/requests/test_validated_fields.py`
**What it delivers:** Every client's attach request is rejected with 422 when the association name is whitespace only or longer than 80 characters. A missing name stays missing.
**Interfaces:** Consumes `NameField` (existing, same module) / Produces `AssociationNameField`, used by Task 3 as `AttachFileRequestV2.association_name: AssociationNameField | None = None`
**Decisions:** A reusable annotated type, not a validator on the request model, so the rule holds for every model that carries the field. No default: a blank name is omitted, never replaced (decided by the person in scope.md).
- [ ] Write the test `test_association_name_rejects_a_whitespace_only_value`
- [ ] Run `poetry run pytest tests/models/requests/test_validated_fields.py -k association_name` and read the expected failure: `ImportError: cannot import name 'AssociationNameField'`
- [ ] Implement
- [ ] Run it green
- [ ] Commit: "Reject a blank or over-long association name at the API boundary"

    AssociationNameField = Annotated[
        str, StringConstraints(strip_whitespace=True, min_length=1, max_length=80)
    ]

## Task 3: Forward the association name to the upstream call
**Files:** Modify `src/routers/v2/files.py` / Test `tests/routers/v2/test_files.py`
**What it delivers:** A named attach reaches the upstream service with its name. A blank one reaches it with no `association_name` key, so the upstream default applies.
**Interfaces:** Consumes `AttachFileRequestV2.association_name` (Task 2) / Produces nothing a later task uses
**Decisions:** `exclude_none=True` on the forwarded body, not `association_name or DEFAULT`: the backend must not hold a second default.
- [ ] Write the test `test_attach_file_forwards_the_association_name`
- [ ] Run `poetry run pytest tests/routers/v2/test_files.py -k association_name` and read the expected failure: `AssertionError: assert None == 'drawing'`
- [ ] Implement
- [ ] Add `test_attach_file_omits_a_blank_association_name` (`assert "association_name" not in sent.model_fields_set`). It passes before the change, so prove it bites: removing `exclude_none=True` must make it fail.
- [ ] Run both green
- [ ] Commit: "Forward the association name on attach and omit a blank one"

    async def test_attach_file_forwards_the_association_name():
        files_api = SimpleNamespace(attach_file_v2=AsyncMock())
        request = AttachFileRequestV2(file_id="f1", association_name="drawing")

        await attach_file_v2(project_id="p1", request=request, service=SimpleNamespace(files=files_api))

        sent = files_api.attach_file_v2.await_args.kwargs["attach_file"]
        assert isinstance(sent, AttachFile)
        assert sent.association_name == "drawing"

## Task 4: Verify (run by Software Validation)
- [ ] `poetry run pytest tests/` and `pre-commit run --all-files`, with counts against the parent commit.
- [ ] Against the development environment, the one row no test here reaches (the upstream default name): attach three files through the generated client: named, blank, whitespace-only. Expect 201, 201 with the upstream default name, 422.

## Out of scope (do not implement)
- A list of allowed names. Renaming an existing attachment. Any change to the upstream service.

## Self-Review notes (resolved during planning)
- Revision 1 trimmed the name in the route. Moved into the type, so every model that carries a name gets it.
```

Rules:

- Ground the plan in the repository's idioms before writing it: introduce no pattern, abstraction or naming convention that is not already present in the codebase.
- No placeholders. "Similar to Task 2" is not a step, and "handle errors" is not code.
- The closing task names the commands for the whole change and runs live only what the proof table says no test can prove, each row with what to call, what to compare (query history, response shapes, counts) and what result to expect. Software Validation runs it; the builder runs each task's own test. A bug the live rows find gets a regression test built from the real object type, for example a result row that turns out to be a tuple subclass, not a dictionary.
- The plan is consumed in small pieces. When a task is handed to another agent, the prompt carries the task identifier, the plan's path and section, the branch, what the plan does not capture (a known broken state, scaffolding that will be thrown away, what the next task fixes) and the success criteria. The prompt does not paste verbatim source code, full test files or full markdown scenarios: the plan already contains all of that. So the plan must be complete enough to be read one section at a time.
- The commit subject names the effect, as a capitalized sentence: "Write bronze batches idempotently with a whole-batch exceptions fallback". Subjects that show the granularity: "Add the RecentOrdersLoader skeleton with an empty-keys guard", then "Build the window SQL with a sorted IN clause and a row number cap".
- Each task can be understood alone: its structure, its classes and interfaces, their relationships as an arrow sketch, its decisions and trade-offs. The bar is that the reader understands the design, decisions and tradeoffs of each task from your walkthrough alone.
- No cap on the number of tasks. A design genuinely too large to be a single coherent piece of work is a signal to decompose it into multiple tickets. Say so under "Open questions for the Software Design Review" and propose the split; do not shrink the design to fit.
- When tasks span repositories, each repository is one pull request, in strict order: a task starts only after the previous one has merged and the relevant SDK has been published.
- A small problem found on the way in the same repository (a missing check, a README line your change makes false, an import your change orphans) is a task here when it is under about two hours of work. A larger one is a line under Out of scope with the reason, unless it belongs to what this ticket delivers; then it goes in the batch with what it costs, doing it now first.

### Questions to ask here

- Can each task be committed with its own test green?
- Is the commit subject the effect, or the file name?
- Where is the code? "Similar to task 2" is not a step.
- Which test covers each edge case in Review focus? Name it.
- Why is this task in this ticket? If the change is not relevant, remove it.
- Does this script need to be stored and committed now, or can it go?
- Is the style rule in the constraints, or are we hoping the builder remembers?
- What is out of scope? Write it down.
- Too big for one ticket? Split it, do not cap it.
