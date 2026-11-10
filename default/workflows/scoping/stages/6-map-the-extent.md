Name what the change touches and who relies on it: systems and contracts, not call sites.

1. **Repositories.** List each with the reason it changes. Named by the ticket or the project: write it. Forced by the change and named by nobody: put it in the session's batch, saying what happens if it is left out. Never add it silently.
2. **Systems.** One line each, by role (a REST service with generated clients, a backend for a frontend, a data pipeline, an infrastructure module): what changes in it that a person or a consumer would notice.
3. **Contracts and consumers.** For each interface, stored shape, event, file format, configuration name, status code or shared name the scope touches: who relies on it, and whether it is extended, changed or removed. Search the sibling repositories you can reach, and name each consumer found. The form is "none found in the three repositories searched"; a bare "none" is not a result.
4. **Removals.** For anything removed: the consumers named, deprecation first, the move of each consumer as its own ticket, the removal last (expand and contract). Write that order.
5. **Order across repositories.** The owner of the contract first, then its consumers, then the surface people see. Each pull request will carry that order, so a change across repositories can be followed.
6. **Operations.** Preconditions and their order ("seed, then deploy"; the datastore applied before the application; an apply timed against the cutover of a consumer, not against the merge); steps a person must run before or after the merge; a rollback that is not the mirror of the rollout; what happens to existing data, and what an upgrade loses once, if anything; what a person must set up (access, a secret, an approval). For existing data prefer, in this order: a reader that falls back for old records over a rewrite of the records; a new table beside the old one, under the same logical name, over a rewrite in place; an added field over a changed one; a reader gated on the version of the producer; a renamed setting that keeps its old name as an alias. When undoing loses something, it goes in the batch: "a rollback after cutover is silently lossy."
7. **Environments.** Every environment, region and restricted site the outcome must hold in. Say what can be observed there today; a measure the system cannot yet produce is a named gap, never a promise.
8. **Cost.** Anything paid for, with its monthly price and what drives it ("about USD 900 per month for the brokers"). Whether a cost is asked follows the money line of the rule on when to ask: a new vendor, service or plan always; a cost inside an existing account only above its thresholds; below them it is decided and written here with its number. A sandbox gets the smallest and shortest of everything, because sandbox data is disposable.
9. **Settle before first use.** Every name that will be applied to live infrastructure, published, or seen by another system; the meaning of every state the scope adds. When settling it is the person's (an address, a promise, a number or wording people will hold them to), ask it in this session with the other questions and record the answer under Decisions; this line then names the settled value, never a decision left for a later stage.
10. **Security and data.** Whether identity, permissions, secrets or personal data are touched.
11. **Base branch**, when it is not the default branch.
12. **Findings that belong elsewhere.** One line each, with the owner; recorded as a known gap, not fixed here.
13. **Design workflows with work to do.** Product Design: a surface changes. A surface is what a user of the product meets: a screen, a page, a dialog, a message or an error, an email, a command's output, a setting, a policy a visitor reads, a tool's description or answer an agent reads. Source comments, README and contributor documents, CI output and workflow files are the repository's documents, not surfaces; Code Design and the build own them. System Design: a component, stored data, a contract, infrastructure, configuration, identity or the release order changes. Code Design: code changes. For the others write "nothing to do" with the reason, so each writes its three lines and passes the ticket on; "nothing to do" must be true of every item under In.

A problem found on the way in the same repository goes under In as "also fixed here" when it is under about two hours of agent time; do not ask. A larger one that belongs to what this ticket delivers goes in the batch with what it costs, doing it now first. A larger one that does not is one line under Out, with no question.

The Extent section, as a template:

```
Extent
- Repositories: [name]: [why it changes].
- Systems: [role]: [what changes that someone would notice].
- Contracts: [contract]: [extended | changed | removed]; consumers: [names, or "none found in ..."].
- Order: [first], then [second], then [third].
- Operations: [step]: [who runs it], [what proves it]. Rollback: [what undoing does].
- Environments: [...]
- Cost: [price per month and its driver], or "None".
- Settle before first use: [name or meaning], or "None".
- Security and data: [what is touched], or "None".
- Base branch: [branch], or "the default branch".
- Belongs elsewhere: [finding]: [owner], or "None".
- Design workflows: Product Design [yes | nothing to do: reason]; System Design [...]; Code Design [...].
```

Filled in:

```
Extent
- Repositories: orders-web (named by the project): the orders page and its export service.
- Systems: a web application with its own backend: exports finish for any list up to 1,000,000 rows.
- Contracts: GET /exports/{id}: extended (a "running" state is added); consumers: the orders
  page; none found in the two sibling repositories searched (billing-web, reports-jobs).
- Order: one repository, one pull request.
- Operations: none before the merge. Rollback: exports running at the moment of a rollback are
  lost and the person starts them again. Accepted by the person.
- Environments: staging and production.
- Cost: None expected. Assumption: finished files fit in the existing storage at 24 hours of
  retention; depends on System Design's choice of where files are kept.
- Settle before first use: the state names "running", "finished", "failed" (the orders page reads them).
- Security and data: a person must see only their own exports (anti-goal).
- Base branch: the default branch.
- Belongs elsewhere: reports-jobs reads the same list with no row limit: its owner; added to
  the status paragraph of its README as a known gap.
- Design workflows: Product Design yes; System Design yes (a stored state, a retention); Code Design yes.
```

**Questions to ask here**

- Which repositories? Named, or inferred?
- Who else calls this? Did you grep the consumers?
- Is there a new environment variable to add to the Terraform configuration?
- Does this work in the restricted cloud regions as well?
- Where is the configuration for an offline site set?
- What happens on rollback? Same as before, or lossy?
- Is this ours to fix, or theirs?
- What does someone have to do by hand, and when?
