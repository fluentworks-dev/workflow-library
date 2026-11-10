Command-line output, tool messages, operator logs, notifications, emails, release notes, runbooks and documentation are surfaces. Design them like screens: a goal, a path, every state, exact words.

**Operators**

- The rules for states hold for messages: an event tells "finished" from "error", a batch lists each failed item, a dialog names the missing files.
- Send warnings through the platform's own logger, so that they land with consistent formatting.
- Do not flood. A recurring fault logs at most once per interval (one line per 10 seconds) and a failure in the shape of the data once per batch, not once per record.
- A failure notification must reach someone: failures of a whole run reach nobody unless the job itself notifies. Address a team address or channel, never one individual.
- Write what changed for the operator apart from what changed for the developer, with a "Breaking changes" part that gives the exact configuration text to paste.
- A runbook is the interface for the worst day: exact commands, the expected result of each, the condition that proves it worked, and a table from symptoms to causes. Write it the day the failure is first seen.
- What only a human can do is written as exact steps in order, each with its proof (seed, then deploy). State a consequence that is not symmetric: a rollback after cutover is silently lossy.
- Write each document for its one reader. A file read by agents keeps sentences as short as possible without losing information. A README carries what a human operates, with a status paragraph saying what is live and what is known to be broken.

**When the user cannot debug**

When building for non-technical users, the product carries the complexity. The promise reads like this: build your application locally, ask for it to be deployed to the cloud, and the platform takes it from there, with no cloud or operations knowledge required.

- Ask exactly one question (the name of the application) and infer the rest.
- Fix it before saying anything: the user is non-technical and cannot debug, so when something fails the tool tries to fix it itself before saying anything, retries a given fix a bounded number of times (about 2 to 3), and always speaks in the domain's plain language.
- Never show a stack trace: never let raw exceptions reach the client. Redact for the user and write the full failure to an audit record.
- Every error has a domain, a message and a remedy: "The name 'X' is already taken by another app." then "Pick a different app name."
- Fail at the first step that can tell: a misconfigured key fails in the first identity probe, not as a cryptic failure in the middle of the deploy.
- Split the irreversible step from the reversible one: creating an empty repository publishes nothing; before a directory is pushed the user is told in plain language exactly what would be published.
- Onboarding can be run twice: if something is already installed or registered, treat that as success and continue, do not fail. Say what each probe result means: for example, 401 means a bad key, and any other code, such as 406, means the key was accepted.
- Name a human to turn to, in every document and in the last line of a failure the tool could not fix.
- Prove it before announcing. The platform deploys itself through the pipeline it ships, and throwaway canary applications go through first, so their failures become the bug list. After a real run on an unfamiliar machine, write a severity table (P0, P1, P2) in which each change maps to something that actually went wrong during that run, then a "Considered but not changed" part with the reason for each.

This is the golden path practice of developer platforms.

**When the user is a developer**

- Entry points anyone can read: thin, literal files, such as three short triggers that call one shared job.
- A fresh checkout runs: defaults point at a safe sandbox, and what a newcomer must be granted is listed with the error they see without it.
- A missing piece fails with its name: a new required value touches four listed places and the documentation table in one change, and missing one fails the deploy with a clear "Missing required environment variables" message.
- Example before explanation: if writing a schema needs more than the examples in the design document, the design has failed.
- Generated client code reads as a sentence (`projectsApi.createProjectV1(...)`). A call a human writes by hand uses named arguments: the keyword form does not require remembering signatures, and it lets a signature grow without reordering anyone's call.
- The repository's guidance file lets an agent or a new engineer act without asking. A setup step that bites is written down the same day, with the error it prevents.
- Port a sibling repository's pattern and say which one.
- Practice for command-line output: results on standard output, diagnostics on standard error, a non-zero exit on any failure, `--help`, `--dry-run`, no meaning by color alone. A command that reports "2 of 3 uploaded" and exits 0 has lied to the script.

### In practice: Bash and command-line tools

Applies when the repository uses Bash scripts or a command-line framework such as Typer or argparse.

- Test arguments with a usage message (`${1:?Usage: ...}`), list prerequisites in a header, and print the configuration before acting.
- A required value that is missing fails before any work and names every missing one: "Missing required environment variables".
- A script that changes data can be run again and has `--dry-run`.
- The logic raises small named exceptions; the command layer turns each into one sentence and a non-zero exit (`typer.echo(str(error)); raise typer.Exit(1)`). The logic imports nothing from the command framework.

### In practice: ROS 2 and robots

Applies when the repository uses ROS 2 and the user is an operator at a robot.

- The node must not die and the cloud must not block a run: the operator can still start a run when the upload failed. The written exception: when a run is tied to a job record, a missing cloud record would silently detach the run from that record, so that failure stops the start.
- Validate operator configuration at startup, never in the middle of a run. Warn through the ROS logger and fall back to the default. Refuse unknown keys: typos must surface rather than being silently ignored.
- A value that selects an environment has no built-in default, because a wrong one silently points a robot at the wrong environment. A tuning value is optional, with its default documented.
- A file an operator may have to rescue stays readable with shell tools (`zcat`). A file name another service depends on carries a warning in capitals where it is defined.

### In practice: GraphQL

Applies when the repository uses GraphQL as a read-only layer that humans and dashboards explore.

- Field names match the sources one to one; a consumer that wants another name aliases it.
- Custom scalars make values readable (`DateTime`, `Date`, `Minutes`, `ByteSize`). An identifier that is not a primary key is a `String`, for a better filtering experience. A field owned by another system is a string, not a closed list.
