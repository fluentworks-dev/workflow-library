Run each row of the plan, the riskiest first, and record as you go. Memory is not a record.

1. Start what the route needs: a free port, a fresh data folder, data made for testing. Record the process identifier of everything you start.
2. Run the route with the method for its kind, below. Record the exact command or steps, what it printed, the counts, and a screenshot of anything a person sees.
3. Read the result against the "Fails if" you wrote. Mark the row proven, failed or unproven. A row you could not run is unproven, with the reason.
4. Run the whole check set once at the branch head and once on an untouched checkout of the parent commit: this is the pipeline's one deterministic run with counts, since the builder and the reviewer ran only the tests of what they changed. Write every count into the test plan with its baseline beside it, and name what fails only on the branch.
5. Run every other command and live row in the pull request's test plan. Check a box only for what you reproduced; uncheck one you cannot.
6. Run every repeatable operation a second time and record what the second run did.
7. Close with the manual verification. Against a development environment, compare what the system did, not only what it returned: the query history before and after, the shapes of the responses, the rows written. Keep the transcript.

Do not stop at the first failure unless it blocks the rest. Record it and go on, so that the fixes are made with everything in view. Fix nothing yet, except what stops you from running. Wait for the condition you need (the server answers, the file exists, the job reports done) with a timeout, never a fixed sleep. Keep screenshots, transcripts and logs as evidence on the pull request or the ticket; do not commit them.

A line of evidence has the command, the count and what it was checked against. The register:

- "`poetry run pytest tests/` (412 passed)"; "Playwright, Chromium: 63 passed"; "`terraform test` in all 10 modules: 64/64 pass"
- "Verified against the full source (all 52,000 rows)"
- "verified against the installed 0.4.0 SDK; is_required() is True for all four fields"
- "`subnet_ids` is `set(string)` in the pinned provider version, verified from its schema"

A bare "Tested locally" or "Tested on the device" proves nothing to a reader. For a stored result, a screenshot of the stored document does.

**Seen**

- Drive the built product, not a component on its own: the preview or packaged build, in a real browser or the real window, with a fresh data folder and a test account.
- Walk the path in product-design.md and visit every state it names: empty, loading, error, one, many, long values, no permission. Capture each, at real sizes: a phone width, a name of the maximum length, a list longer than a page.
- Walk it once with the keyboard only. Every control has an accessible name; if you cannot select a control by role and label, add the name. Check the surface against WCAG 2.2 AA.
- Capture what the surface sent, not only what it showed. A blank optional field is absent from the body, not null and not empty, so the upstream default applies.
- Fail the backend and read the wording. A failure the person can act on shows the server's sentence; an internal failure shows a general sentence and never the server's detail; what the person typed is still there. A value the surface does not know is shown raw, and a field that cannot be changed is shown grayed with "cannot be changed", not hidden.
- Read each message as the person who must act on it. The wording to expect: "This item does not exist or you do not have access to it." (a 404 never reveals which); "The server ran into a problem. Try again, and contact support if it keeps happening."
- Compare with the design file and list every difference as fixed, or as settled by a decision the person made.
- For output without a screen (a command, an operator's log line, an email), run the real command and read the text as its receiver. No stack trace reaches a person who cannot debug it.

**Interface**

- Start the real service and call every route the change touches over the wire, each with its expected status written first. Call them again through every generated client, because consumers use the client and not the wire. The line to earn: "the affected v3 endpoints exercised via direct HTTP and through both the generated Python and TypeScript SDKs ... 36/36 each".
- Regenerate the committed interface description; a stale one must fail the checks. Diff it against the default branch as a consumer would: a removed route or field, a tightened type or a changed status is a break until proven otherwise.
- Older versions stay exactly as they were, even where they look wrong: "Legacy clients depend on this; do not 'fix' it". Assert the frozen status of the old version beside the new one.
- Run the previous released client, or the version the consumer pins, against the new provider.
- Errors are part of the contract: each has its status and a message that stands alone. Where a design says existence must not be revealed, a denied read of one item answers as not found. A write that would strip the caller's own access answers 403, "so clients can tell a permission problem from a validation problem", and a denial "opens with 'Request denied:' because clients may show it without any surrounding heading".
- For a backend that fronts another service: an upstream 4xx passes through with its status and detail unchanged; an upstream 401 or 5xx becomes 502 with a request identifier and never the upstream detail.
- Lists: page through while inserting, and nothing is skipped or duplicated. Test the default limit and the cap at, below and above. A filter on an unknown field is refused, not silently empty.
- Call each route as the laziest consumer does, with every optional parameter left out. A list cut off at the default with no sign of more is a finding, and the fix belongs in the server's default, not in every consumer (for example, a default page size raised from 50 to 200).
- Corrupt one stored part: a read that composes several parts returns the rest and logs the bad one; a direct read of the bad part fails loudly.
- Send a stale version marker and expect the conflict. Send hostile input as a field name, a filter value and a sort key.
- Search the consumer repositories for every route, parameter and default the change removes or alters. A consumer that still uses one is a finding; the remedy is to restore it as deprecated and move the consumers first. When a parameter is removed, prove old callers still pass and pin it: "Wire-level compatibility is preserved (FastAPI ignores unknown query params; a router test pins this)."
- When the provider's change is not released yet, test the consumer against the build the provider's branch published, and name it. The swap to the released version stays an unchecked item.
- Practice: consumer-driven contract testing (Pact) automates this pairing. Without it, the committed description, the generated clients and upstream error fixtures copied verbatim do the same job by hand.

**Stored data**

- Run on a copy shaped like the real data, never on the real data: the real sizes, the nulls, the documents every older version wrote, the rows that already break the new rule.
- Compare values, not only counts. The line to earn: "Backfill in production (lookback 365 days): 52,000 rows, identical to the source warehouse with 0 missing, 0 extra, and 0 value mismatches". A small table is enough when it holds the odd shapes: "Verified locally with a 5-row table".
- Use the dry run first. Run it twice; the second run changes nothing. Interrupt it and run it again; the end state equals an uninterrupted run. Run it beside a live reader and writer when it must be safe beside the live service.
- Test both directions: the default branch's code reading the new shape, because the old server keeps serving during a deploy, and the new code reading every old shape. A read-time fallback, added "so existing data keeps working without a backfill job", is tested with a document that predates the field.
- An additive change (a nullable column, a new table, a new version of a table under the same logical name) is proven by the old reader still working. A drop or a rename is proven by showing nothing reads the old name, here and in the consumers.
- Practice: expand and contract. Add first, move the readers, remove last, each in its own change; a removal never ships with the code that stops reading it.
- A timestamp that is compared as a string is stored with `+00:00`, because "only `+00:00` sorts chronologically against fractional seconds". Sort a mixed set and check.
- State what a rollback keeps and what it loses. "a rollback after cutover is silently lossy" is something the person must read before the merge.
- Where the pull request template asks whether a backfill is needed, answer with what you ran, not with a tick.

**Job**

- Run it end to end on a sample that includes bad rows, through the real entry point, with the configuration of a development target.
- Reconcile: rows in equals rows out plus rows rejected. Every rejected row is kept with its raw bytes and the reason; when a whole batch fails, the whole raw batch is kept. Nothing is dropped silently, and a bad record never breaks the pipeline. Practice: this is the dead-letter queue pattern, and a reconciliation count belongs in the output of every run.
- Replay the same input, and interrupt between writes.
- Feed the shapes production feeds: a batch in which every record lacks an optional path, a wrong type at a known path, each producer version including none, late records, records dated in the future, duplicates.
- Check the output's schema, not only its rows: types, nullability, and that a column missing from every record kept its type.
- When the producer's output changed shape, a changelog per producer version with a column map comes before the reader changes. Test the reader against each version in it.
- Run it twice on the same input when it claims to be deterministic; an aggregate that takes the "first" value is not, so expect a maximum. A port matches the old job byte for byte; the old code is the specification.
- A failure at the level of the run (the cluster never starts, the run times out) must notify someone; a failed run that emails no one goes unnoticed.
- Read the plan of the heaviest query for a full scan where a partition filter was promised.

**Infrastructure and configuration**

- Offline checks first, then the plan as the acceptance test: write the expected counts, then compare ("12 to add, 3 to change, 0 to destroy"). An unexpected replace or destroy fails the change. Plan every environment the change can reach, and name each drift separately.
- You never apply and never destroy, and you never loosen a guard for a test: "Do not flip these inputs to make a destroy pass". Write the apply as exact steps with the expected counts; after the person applies from the branch, the plan "must report no changes":

```
1. From the branch, in `live/sandbox/queue`: `terragrunt plan`. Proves it worked: "3 to add, 0 to change, 0 to destroy".
2. `terragrunt apply`, then `terragrunt plan` again. Proves it worked: "No changes."
```

- Start the real process with the new configuration, in the shape of each environment.
- A value that selects an environment (a URL, a client identifier, a tenant) has no default in code: remove it, and the start fails naming the key. A value that only tunes behavior is optional: remove it, and the documented default applies. A flag that turns a feature off and an input that is merely absent mean different things: test both and expect different behavior.
- A typo in a selector fails at load. An unknown key is refused. A malformed file fails at configure time, never inside a background thread in the middle of a run: "Parse the run settings up front so a malformed file fails fast at configure time instead of surfacing inside the background upload thread"; "Typos must surface rather than being silently ignored".
- The shipped template loads. A production secret cannot resolve to a development default: the production target overrides it with a placeholder such as `ignored-placeholder-to-prevent-default-value`. A new required variable exists in every place the deployment reads it. A renamed setting still accepts its old name. A value for a browser bundle is tested in the built bundle.

**Library or tool**

- Build the package and install it into a clean environment as a consumer does. An editable install proves nothing about packaging.
- Write a small consumer that follows the first example in the documentation: "If writing a schema needs more than the examples in this document, the design has failed".
- Drive commands through the real entry point: output, exit code, the message for a bad argument, the help text. An error is one sentence and a non-zero exit code.
- When the design promises a core without dependencies, check that the import pulls in nothing outside the standard library. A wrong definition must fail at import "with a message naming the schema and field": write one and read the message.
- Consumers of the previous release still work. Any change in nullability counts as breaking.
- A shell script is run for real: with no arguments, with each required variable unset, with its dry run, twice, and killed midway. Practice: run `bash -n` and ShellCheck where the repository has them; neither replaces running the script.

**Device**

- Report three rungs separately: the logic without the runtime, the simulator, the real device by a person.
- The probes are physical: the network drops mid-upload, the disk fills, the run lasts three days, the device restarts mid-write, the cloud is unreachable at start.
- The process that controls hardware must not die, and the cloud must not block a run. Raise inside every outward call and assert that the process survives, logs and reports the failure. Where a design names the one case that must fail hard, test that it does. Test what the operator sees as well: the event says "finished" or "error", and a failed upload lists each failed file with its error.
- Show retention by arithmetic from a sample (a 21-minute run extrapolated against a 72-hour requirement), and recovery with the shell command the design names (`zcat` on a truncated file). A stream that ends right at the rotation cap "leaves no empty trailing file".
- What needs the device stays an unchecked box with exact steps: "Test on the device in offline mode". An approval given "pending testing on the simulator" does not check the box; the run does. When you do not know how an environment is set up, find out or ask; do not guess.

**Performance**

- Measure before and after, the same way, with units, and put the numbers where the change is described: "cold 210 s to 11 s, warm 183 s to 0.8 s".
- Measure the baseline on the parent commit first, in the same environment, with the same data and request. Measure cold and warm separately: the cold number is what a person meets after a restart.
- The most truthful measurement is in production. You measure in the closest environment you may use, and leave the production measurement as an exact step for the person, with the command and the number to compare against.
- Count as well as time: queries per request, rows and bytes returned, calls per item. A count can be asserted in a test: two concurrent lookups produce exactly one query.
- State the limit of the fix beside the gain: "this only stops the bleeding ... The durable fix is repartitioning + OPTIMIZE", on the side that owns the data.
- When a threshold was chosen by feel, say so: the numbers are based on an intuition of what is reasonable.
- Proofs for the usual fixes:

| Fix | Proof |
|---|---|
| One query per item collapsed into one batched query | One query for N items; siblings that ask at the same moment share it |
| A cache keyed on a rendered query | The same keys in a different order give the same key, because sorted key lists make the rendered SQL a stable cache key |
| A filter on the partition column | The plan prunes; a key with nothing inside the window still resolves through the fallback |
| A cache with a size and a time to live | The entry expires at the stated time, the cap holds, and the comment gives the reason for both numbers |
| A cache per request, or a client built once at start | N parallel reads cause one fetch; no token fetch and no handshake per request |

- A mitigation that does not work is removed, not tuned. A throttle that does not work is removed as soon as that is shown, and the repeated cost is fixed instead. Reliability beats throttling; measurement beats theory.
- Practice: repeat each measurement at least five times and report the median and the 95th percentile; keep the machine, the data and the concurrency fixed; count calls while idle too; leave a test that asserts the count where a regression would be costly.

**Platform**

- When shipping a platform, a pipeline, an installer or a tool that other people will run, run it through its own pipeline and push canaries through it before anyone is told, so that you meet what bites before they do. Use throwaway applications of different shapes (one with a native module, one static page); what breaks is the list of fixes.
- Run on the machine the audience has, not yours. Report field findings by severity (P0, P1, P2), where "Each change below maps to something that actually bit during that run", then "Considered but not changed".
- Test as the person the tool is for. For a person who is not technical: it asks exactly one question, confirms cost before creating anything, tries a bounded number of fixes (about 2 to 3) before saying anything, speaks by domain in plain language, never shows a stack trace, and names a human to turn to. The rule the tool follows: "The user is non-technical and cannot debug. When something fails, try to FIX it yourself before saying anything". Break something and read what that person would read.
- Errors carry a domain, a message and a remedy: "The name 'X' is already taken by another app." / "Pick a different app name." A wrong key fails early, at the identity check, "instead of a cryptic `gh repo create` failure mid-deploy".
- Onboarding is idempotent: "if something is already installed or registered, treat that as success and continue, don't fail". Run it on a machine where half of it is already in place, and check that each probe means what it claims: "401 means a bad key; any other code, e.g. 406, means the key was accepted".
- The irreversible step is split from the reversible one: the step that publishes first says "in plain language exactly what would be published". Walk to its edge and confirm nothing was published before it.
- A guard that blocks a command is a result to report: "do NOT silently retry the identical command, and do NOT try to reword it to slip past the check".
- A canary that creates cloud resources reaches outside this workspace. Use the sandbox the repository names; when there is none, write the canary as exact steps for the person.
- Practice: this is dogfooding and the canary release. A smoke test after every deploy is the smallest canary, and a canary that stays deployed keeps catching regressions.

Keep an evidence log, one line per run:

```
<#> | <command or steps> | <what was seen, with counts> | <proven, failed or unproven>
O2 | `curl -s -X POST localhost:8123/files/stage -d @blank-name.json` | 200; the upstream call has no `association_name`; stored name "default" | proven
O3 | the same request with a name of 81 characters | 200, expected 422; name stored cut to 80 | failed
checks | `poetry run pytest tests/` | 131 passed, 1 skipped (baseline on the parent commit: 126 passed, 1 skipped) | proven
```

### Questions to ask here

- Did you run it, or did the tests just pass?
- What did it print? How many?
- How many rows in, how many out? Any missing, extra or different?
- Has it been tried in the development environment, and how does it look?
- What was the count before this branch?
- Is that failure ours, or does it fail the same way on the parent commit?
- Cold or warm? Measured where?
- Is this the smallest instance type that does the job? A sandbox needs only the smallest.
