Write the Outcome section as observations someone can check (the definition of done), numbered O1, O2, ... and never renumbered, so later documents can point at them.

**Outcome, not mechanism.**

| Mechanism (do not write) | Outcome (write) |
|---|---|
| "Add a `status` column and a nightly job" | "A person sees whether an import finished, failed or is still running, at most 5 minutes out of date" |
| "Put a red banner at the top of the page" | "A person who lacks access is told so, and told whom to ask" |
| "Run exports on a background worker" | "An export of up to 1,000,000 rows finishes, and the person can leave the page while it runs" |

**Name the person, then add the outcomes tickets forget.**

- An operator at a machine: what keeps running when the cloud or a side task fails, and the one case where a failure must stop the start (a missing record that would silently detach a run from its launch); how "finished" differs from "failed"; each failed item of a batch listed with its error; which files are missing, by name; that one malformed item does not hide the rest of a list.
- A user of an application: what they are told on a failure and what they can do next, in two classes: the sentence they can act on ("This item does not exist or you do not have access to it.") and the one for a server fault, never its detail ("The server ran into a problem. Try again, and contact support if it keeps happening."); that a server error stays visible while they correct the form; that doing nothing is safe (blank sends nothing, clearing a value keeps the stored one); that a fixed field is shown as fixed and an unknown value is shown as it is.
- A non-technical user of a tool: how many questions they are asked (one); what they see when a step fails (the domain, what happened, what to do; never a stack trace); that a wrong setup fails at the first step that can detect it, instead of a cryptic failure halfway through a deploy; what they are told before the step that cannot be undone and before anything is paid for; that setup can be run again; that a blocked action is reported to them and never retried in other words; who the human to go to is.
- An engineer or a system downstream: what stays as it was, what is deprecated and not removed, and what they must change, in which order. Old names survive a rename as aliases; a mapping keeps their schema stable while the source changes; a changelog per producer version, with a map of what moved, comes before any reader changes.
- A future maintainer: a status paragraph that says what is live and what is known to be broken; a runbook written the day a failure is learned; every opaque identifier named beside its value; the alternative that was rejected, written where someone would be tempted to undo the decision.
- A developer or an agent working in the repository: a workflow anyone can read, one documented list of every place a new setting must be added, a guidance file that lets a newcomer act without asking.

Use Given, When, Then for an outcome that has a precondition, with nothing but an observation in the Then.

**Pin every number.** Each carries a unit and a reason:

| Kind | The question to answer in the outcome |
|---|---|
| Freshness | How old may what a person sees be? |
| Window | How far back does it look, and what about what falls outside? |
| Settlement | When does a period count as final? |
| Size limit | What is the largest thing accepted, and what is the person told at the limit? |
| Retention | How long is it kept, and how much space is that? |
| Ceiling | How many is "many"? |
| Default | What does a caller get who does not say? |
| Cost | What does it cost per month, and what drives it? |

- "Shows running jobs" cannot be tested. "Shows a job as running when its last status is under 5 minutes old" can.
- A number chosen by feel says so, and names what it was compared with.
- A requirement too large to test directly (72 hours, a year of data) is proven by a measured sample and an extrapolation, with both numbers stated.
- A claimed gain carries the number before, the target, and what the change does not solve.
- An abnormal case ends in a refusal someone sees. Waiting that is part of normal use is a state, not an error.

A pair, for the register:

```
Bad:  Exports should be faster and more reliable for large lists.
Good: O1. An export of a 200,000-row list finishes; the file has 200,001 lines.
      O2. An export above 1,000,000 rows is refused before it starts, with a message that
          states the limit and suggests a filter. (Ceiling chosen by feel; the largest list
          today has 212,000 rows.)
      O4. When an export fails, the person is told that it failed and can start it again.
```

**Questions to ask here**

- What number? What unit? Why that number?
- Who is on the other side of this? What do they see when it fails?
- Is that an outcome, or a mechanism in disguise?
- Is missing a few records acceptable for now? Then write that down.
- Is waiting normal here, or is it an error?
- What does someone see when there is nothing? When there are 100,000?
- Could a reviewer check this without asking what was meant?
