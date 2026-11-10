**Look for what already solves it** before designing: a built-in solution of the technology (change data capture for change tracking, triggers for audit trails, framework middleware for authentication), an existing component, a sibling repository. Official documentation first, community examples second; exhaust existing solutions before building. Pick mature, widely adopted options. The standard to beat is a design that reuses an existing service's authentication middleware, managed identity and database client, and adds no new cloud resources. Link what you reuse.

**Sketch two or three structurally different designs.** One of them is always the design that adds the least. Before a design adds something, rule out the smaller form in writing:

| Before adding | Rule out first | What it costs, to count in the design |
|---|---|---|
| A database engine | A table or collection in the store that exists | Backups, migrations, credentials, and two writes that cannot be atomic |
| A table | A field on an existing record; a value computed on read | A second home for a fact |
| A service or worker | A task in the existing process; a scheduled job; the same worker on another queue | A deploy lane, configuration and an identity in every environment; two versions during every release |
| A queue | Returning at once and letting the caller poll state that exists | Delivery semantics, a bound, a place for failures, a depth to watch |
| A cache | Removing the repeated cost | Staleness a user can see, and invalidation |
| A mode or flag | The same code with different configuration | Two behaviors to keep in step |
| A vendor | A built-in feature of the platform already in use | A bill, and a region and compliance question in every environment |

**When you propose the smaller form, enumerate what it removes.** For example: the change can be made without new machinery, and that removes `version_history.py`, `_find_open_drafts` and the re-read after create. **When a mechanism is separable, cut it out** and name it as a seam or a later ticket, with the list of what leaves.

**Compare the options in one table**, one row per option. Filled example:

| Option | Adds | Effect on existing consumers | Failure it introduces | Cost | Value, effort, risk (confidence) |
|---|---|---|---|---|---|
| A. Task in the existing process, files in a bucket | One table, one bucket prefix | None: new routes only | A file with no `ready` row after a crash | About 9 USD a month at 100 organizations | High, low, low (high) |
| B. Separate export service with a queue | A deployable, a queue, a deploy lane | None | Two versions during every release; a queue depth to watch | A second always-on instance | High, high, medium (medium) |
| C. Build the archive on every download | Nothing | None | A download that times out on a large project | Compute on every download | Medium, low, high (low) |

Rate value, effort and risk as high, medium or low, with a confidence qualifier. When confidence is low on any axis, treat that axis one level worse.

**Recommend one, first.** Then say why it wins, why each alternative lost, and how the decision could be revisited. Before committing to a recommendation, state the strongest objection to it, and answer it. Adopt the good ideas of the options you reject, and say which. Lead with the most contestable part: order by controversy.

**The option that adds the least wins unless it is wrong.** "Less general" is not "wrong".

**When the choice turns on a fact, get the fact**: read the installed library, run the query on test data, measure. **When the choice is empirical** (a format, an engine, a size), prototype the candidates on throwaway branches and pick by an operational criterion. For a log format, for example, prototype the candidates and let the simplest win when it can be recovered with `zcat` and a size extrapolation meets the run-length requirement. **When porting**, the old code is the specification, branch for branch, proven by parity tests.

**When the design reverses itself twice on one point, stop.** Put both versions in the table and decide once.

**What to ask the person**, in the session's one batch (asked before system-design.md is written) and by the shared rule on when to ask. Only what is theirs: a new paid service or plan, or a cost above the money thresholds of that rule; a new outside dependency or vendor; a destructive or irreversible data change; a choice that changes what the product does, which scope.md did not settle; two shapes that differ in what a person can do, pay or risk, which nothing has settled. Two shapes that differ only in how the system is built are yours, also when neither wins on the facts. Everything else you decide and record under "Decided for you". Example of a question:

> A finished export is a link that downloads every file of a project. Nothing in the product today lets a file leave through a link someone else can open. Who may use the link?
> 1. "Only the person who asked (Recommended)". They download it signed in; a colleague starts their own export. No file reaches anyone without access to the project. The common practice for private data.
> 2. "Anyone with the link, for 5 minutes". Easier to hand to a colleague; anyone who receives it in those 5 minutes downloads every file of the project, and that cannot be taken back.

Where the files are kept (a bucket, the database, or built on every download) is not asked: it changes nothing a person meets, the bucket costs about 9 US dollars a month at 100 organizations inside the existing account, so it is decided and recorded under "Decided for you" with that number.

## Questions to ask here

- would it be simpler to use a different queue? the same worker on another queue may solve it without separate worker modes.
- what does a mode setting add on top of that?
- can a new option, such as a worker count, wait until we know we need it?
- is this required, or optional? can it be simplified for now?
- does the platform already do this?
- what does the version with no new component look like, and what exactly is wrong with it?
