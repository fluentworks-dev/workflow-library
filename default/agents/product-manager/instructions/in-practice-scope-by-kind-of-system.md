### In practice: Python services, REST and GraphQL
Applies when the repository uses a web framework such as FastAPI to serve an API that other repositories call.

- A value added to a closed list that consumers receive is visible to consumers: publish the client, upgrade the consumers, then emit the value.
- A change of behavior for existing callers is a new version with stated behavior. The old one stays, status codes frozen, deprecated, until its consumers move, with a note at the code such as: legacy clients depend on this; do not "fix" it.
- A rule for every client is scoped at the service, so the rule holds for every client rather than only the form in the interface.
- A read-only layer over other stores (the usual place for GraphQL) takes no writes; scope writes in the service that owns the data.
- A contract change consumed through generated clients is at least two tickets in order: the service, then each consumer.
- A write that would remove the caller's own access is refused, naming the attribute and never its value.
- A field owned by another system passes its values through as they are; a closed list here would need a release for every value added there.

### In practice: TypeScript and React web applications
Applies when the repository uses TypeScript with React or similar, over a backend it does not own.

- Read paths first; write paths wait for the product rule.
- A rule the backend owns is not repeated in the interface: suggestions yes, requirements no.
- An accessible name on every new control is an outcome.
- When the application pins a client of its backend, the scope names the backend change it waits for.

### In practice: Spark data pipelines
Applies when the repository uses Spark, Databricks or a similar engine to fill tables other people query.

- Never scope a change that can drop a record; a failing record is kept with its original bytes.
- A changed layout breaks no reader: nothing a reader uses disappears, and readers move as their own step.
- Old data keeps working without a rewrite wherever a reader can fall back. A backfill that cannot be avoided is a step that can run again, with a dry run; its logs are by-products.
- Freshness and settlement are numbers: how late data may arrive and still be counted, and when a period is final.
- Cost levers are their own small tickets: a smaller pool, a schedule made less frequent, a write that touches only the partitions it needs.
- A name that enters a shared catalog is qualified by its owner.
- A change in the output of a producer gets a changelog per version before any reader changes.
- A port is proven by parity: every row of the full source compared, with 0 missing, 0 extra and 0 value mismatches.
- A failure of the whole job must reach a person; a failed job that nobody is told about is a gap in the scope.

### In practice: Terraform and Terragrunt
Applies when the repository uses Terraform or Terragrunt to apply infrastructure to live accounts.

- A person applies and a person destroys. Each apply is a step with the expected plan counts as acceptance ("12 to add, 3 to change, 0 to destroy"). Never scope an automated destroy.
- Names are settled before the first apply; a rename of something applied is a migration.
- Check that a service exists in every partition and region the outcome must hold in.
- When code and the live account may have drifted, the first slice plans every environment and aligns the code to what is live, naming each difference.
- A new required setting lands in every place at once (template, allow list of the script, workflow, environment, documentation): ask whether any new environment variable must be added to the infrastructure code.
- Trust roots, and first applies that need special credentials, stay with a person and are listed as such.
- A failure learned today gets its runbook today, in scope with the fix.

### In practice: ROS 2 and robots
Applies when the repository uses ROS 2, or runs on equipment an operator stands next to.

- "The process must not die" and "the cloud must not block a run" are anti-goals.
- A feature switch and a runtime input that look alike stay separate when they mean different things.
- A setting that selects an environment is required, with no built-in default; one that tunes is optional, with a documented default. A wrong default silently points a machine at the wrong environment.
- Retention and disk are outcomes with numbers. Partial failure is reported per item.
- A check on real equipment or a simulator is person time; what was not run stays visibly unchecked.
- A change the operator must act on is written for the operator, with the exact text to add.
- Defer quickly to the lifecycle rules of the platform and to its experts.

### In practice: Bash and operational scripts
Applies when the repository uses shell scripts for deployment, data fixes or setup.

- A script that changes data or infrastructure can run again, has a dry run, stops at the start naming what is missing, and prints what it will do. A one-time script is deleted once it has run.

### In practice: developer platforms
Applies when the repository uses an assistant or a deployment service to let non-experts do an expert's job.

- The meaning of each probe is written down: "401 means a bad key; any other code, for example 406, means the key was accepted".
- A run on every operating system people use is its own slice; its findings return as a severity table.
