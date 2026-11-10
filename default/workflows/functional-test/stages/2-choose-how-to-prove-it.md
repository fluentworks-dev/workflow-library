Decide what running it for real means for this change, before running anything. Sort each promise by kind. A change that crosses several kinds gets one row per promise.

| Kind | Most realistic proof | Closest route when blocked |
|---|---|---|
| **Seen**: what a person sees or reads (a screen, a command's output, a message, an email) | Drive the built product as a person would, at real sizes, through every state the design names | The surface in a real browser with every backend call intercepted |
| **Interface**: what another program calls | Call the running service directly and through every generated client | The route functions with only the outermost client replaced |
| **Stored data**: a schema change, a migration, a backfill | Run it on a copy shaped like the real data, compare before and after, run it again | A small table built by hand that holds the real odd shapes |
| **Job**: a pipeline or background job | Run it end to end on a sample, reconcile the counts, replay it, interrupt it | Its functions on a local engine with small real tables |
| **Infrastructure and configuration** | Validate offline, read the plan against the counts you expected, start the real process with the real configuration | Offline tests with a mocked provider, and apply steps for the person |
| **Library or tool** | Use it from a fresh consumer, installed the way a consumer installs it | The built package in a clean environment |
| **Device**: a robot or other hardware | The logic without the runtime, then a simulator, then the real device by a person | Offline tests, and unchecked boxes with steps |
| **Performance**: a claim about speed or cost | Measure the same request before and after, cold and warm | A count asserted in a test, and a run on a sample |
| **Platform**: what other people will run | Run it through its own pipeline and push throwaway applications through it | One canary through a development environment |

Rules for choosing:

- Take the row for what the promise is, not for where the code sits. A new column shown on a screen is a Seen row and a Stored data row.
- For each promise pick the highest rung this session can reach, and name the route a person or caller takes through every layer the change crosses.
- Write what you will observe and what would count as failure before you run it. A result read without a criterion written first is an opinion.
- Order the rows by what a failure would cost: money, lost data, security, what cannot be undone.
- When two routes are equally real, take the one the repository already has a command for: a walk script, a preview workflow, a request playbook, a simulator.
- Prefer a proof that is a command someone can run again. The person, the next session and the gate can all rerun it.
- When a live route you build will be needed again, leave it in the repository in the same change, as a script or an agent skill.
- Where the full route needs an account, a paid service, a device, production data or access this session lacks, plan the closest route and write what it leaves unproven. Never plan a route through the person's own accounts, data or settings.

Situations that change the route:

- **More than one repository.** Run the consumer against the build the provider's branch published, and the previous released consumer against the new provider. The second run proves the merge order is safe.
- **A migration or a new setting.** Prove the order the rollout names: the migration first, then the code that needs it.
- **Nothing here can run it** (hardware, a cloud apply, a signed build). Take the offline rungs and write the rest as exact steps for the person.
- **A refactor, a rename or a dependency bump.** Prove parity: the same requests, jobs and checks, before and after, with the counts side by side.
- **A claim about speed or cost.** Plan the baseline run on the parent commit before the run on the branch.

The plan is one table. It becomes the outcomes table of the validation summary.

```
| # | Promise (source) | Kind | Route | Observe | Fails if | Unproven here |
|---|---|---|---|---|---|---|
| O2 | A blank name is left out of the request (system-design.md) | Interface | Backend started locally against the sandbox; direct HTTP and the generated client | No `association_name` key in the upstream call; stored record shows the default | The key is sent as null or "" | None |
| O3 | A name over 80 characters is refused (product-design.md) | Interface, Seen | Direct HTTP at 80 and 81 characters; then the form in the preview build | 200 at 80; 422 at 81; the form shows the server's sentence and keeps the files | 81 is accepted; the form is cleared | None |
| O5 | Upload from the packaged application (test plan, unchecked) | Seen | None here: needs the signed build | Exact steps for the person | Not applicable | All of it |
```

Decide without asking: the route, the sample, the tool, the order. Keep one line per choice for "Decided for you". Find out facts by running or reading: where a setting lives, what a library does, what the gate runs. Ask with AskUserQuestion only when a route would spend money, create something outside this workspace or send something to real people, or when the scope fits two readings of what must be proven.

### Questions to ask here

- How does a real person or caller hit this? Then test it that way.
- Has it been run in the development environment to show it works?
- Why a mock here? Can the real thing run?
- Does this work in every cloud partition and region it deploys to?
- Where is each setting this reads configured?
- What is the closest we can get on this machine, and what does that leave unproven?
- What counts as failing? Write it down before you run it.
