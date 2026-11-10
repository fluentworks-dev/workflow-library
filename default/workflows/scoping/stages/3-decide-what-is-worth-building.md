**1. Write the value.** One short statement a reader could check, in three parts: value (who is better off, and how), output (what exists when it is done), measure (how anyone would know, with a number where one exists). Tie it to the direction of the project. If you cannot name a value, write that down: it is a finding.

Weigh the value; these kinds come first:

- Access: other people reach the data or the platform without asking someone. A query layer over every store, a scheduled feed, a database of definitions shipped as a versioned release, a data lake mounted from a command line with a throughput benchmark.
- A named consumer: the value statement says who it is for ("to support the orders page", "for backfill jobs"). A colleague's one-off need shipped in a day inside something already deployed is good enough for use, and gets a second version when the need changes.
- Compliance: in a restricted environment a dependency removed is value in itself, such as one unnecessary dependency on an outside hosting service. So are identity without stored secrets, unrestricted files routed to a store of their own, and the environment's own approved endpoints everywhere.
- Reliability: the value is the failure gone, not a limit in front of it. A rate limit that does not work is removed and the repeated cost cached; connection errors lead to closing clients per request; an unreliable network leads to retries with jitter and deduplication by checksum, not to a bigger server.

**2. Look for what already exists**, in this order. Record in two or three lines where you looked and what you found; "nothing found" after a real search is a result, and a search that is not recorded is repeated by the next session.

1. The product itself. Is the capability there but hard to find, half built, or behind a setting? Then the scope is "make it reachable", which is smaller and safer than "build it".
2. The platform and the framework: before designing a custom mechanism, check whether the technology has a built-in solution (for example, change data capture for change tracking, triggers for audit trails, framework middleware for authentication). You do not choose the mechanism; you record that a built-in exists, so design starts from it.
3. The other systems of the organization: a sibling repository, a shared library, a service that already owns the rule.
4. Outside: official documentation first, community examples second. Name the document, the version and the date. Never present research you did not do.

An example of this step: a design for a versioned store of device configurations surveyed device management platforms and GitOps before building anything. The option that won reused an existing service's authentication, identity and database client, and added no new cloud resources. Neither surveyed option was adopted, and three of their patterns were kept: desired state against reported state, versions that only increase, and version checks on writes. A read-only query layer was rejected as the home for the writes, because mutations would break what it promises its consumers. Record all three in scope.md: what is reused, what was rejected and why, and what was taken from the rejected.

**3. List the options.** Two to four, real and different, never one choice with decoys. Always the smallest (use what exists, change nothing, or ship only the part that shows information) and the ticket as written. For a symptom ticket, the three from the previous stage. For a large ticket, the narrow first slice. For a ticket about cost, single levers, each with its measured effect, never one optimization pass: a right-sized cluster or pool, a schedule spaced or moved to the night, an hourly job made daily, a write limited to the partitions it touches, one aggregation in place of several counts.

**4. Rate each** on value, effort and risk: high, medium or low, with your confidence. Low confidence moves that rating one level worse: low confidence in high value counts as medium value, and low confidence in low effort counts as medium effort.

- Value: how many people, how often, how much it changes what they can do; or the size of the cost, the risk or the compliance gap removed.
- Effort: agent time plus person time, including what only a person can do.
- Risk: what could go wrong for those who rely on what exists today, what cannot be undone, what is unknown.

| Option | What people get | Value | Effort | Risk | Gives up |
|---|---|---|---|---|---|
| A. Exports of any size finish (recommended) | No failed export under 1,000,000 rows | High, high confidence: 38 of 41 failures | Medium, medium confidence: 1 to 1.5 days | Low: the exported file is unchanged | A Retry control |
| B. Retry button, as written | A way to try again | Low, high confidence: the retry fails again in 38 of 41 cases | Low: 2 to 3 hours | Low | The cause |
| C. Both | A, and a retry for the 3 other failures | High | Medium: A plus 3 hours | Low | Nothing |
| D. Nothing new | Staff keep splitting lists by hand | None | None | None | The ticket |

A second example, where a rule of the product is undecided and confidence is low on one axis:

| Option | What people get | Value | Effort | Risk | Gives up |
|---|---|---|---|---|---|
| A. Show the stored tags on the detail page (recommended) | Everyone sees which tags an item carries | High, high confidence | Low, high confidence | Low | Editing, until the product decides who may edit |
| B. Show and edit tags | A, and owners change tags | High, medium confidence | Medium, low confidence: counts as high | Medium: a wrong edit removes someone's access | Nothing |
| C. Nothing new | Tags stay visible only through the API | Low | None | None | The reason for the ticket |

A wins: the read path ships, the write path waits, and the limit travels with it: display only, no creating or editing until the product decides who may edit.

**5. Recommend one, first.** Prefer the narrow option that delivers the value now. Then write the strongest objection to it as its best advocate would (imagine the work shipped and failed: why?), and answer it with a fact:

```
Recommendation: A.
Strongest objection: the 3 failures that were not about size still have no way out.
Answer: 3 in 30 days, and each succeeded when the person started the export again from the
page. A Retry control for them is option C, 3 more hours; left out until those failures have
a named cause.
```

**6. Check the proportion.** When the cost is far above the value, recommend less or nothing, and say what the person gives up.

**7. Decide or ask.**

- When the options differ in what the product does or leaves out, in money, or in something hard to undo: it is the person's. It goes in the session's one batch, asked before scope.md is written, recommendation first, each option with its benefit, its cost (rough size, and money by the rule's money line) and its risk. With several decisions, the most fundamental and contestable goes first.
- When one option is plainly right and any competent engineer would agree: take it, and record it under "Decided for you".
- When the recommendation is not to build: ask it at once, before the other stages, since nothing else is worth doing until it is answered. Stopping a ticket is the person's decision.
- When an option changes what the ticket says it is, say so inside that option ("the ticket is retitled 'Exports of any size finish'"). The answer is then the approval to revise the ticket in place.

The question, as a template:

```
Question:  [The situation in one or two sentences, with its cause.] [The question.]
Option 1:  "[What people get] (Recommended)". [Benefit, cost and risk, in about 25 words; rough size.]
Option 2:  "[What people get]". ...
Option 3:  "[What people get]". ...
```

Filled in:

```
Question:  The ticket asks for a Retry button on failed exports, but 38 of the 41 exports that
           failed in the last 30 days had more than 50,000 rows, and those fail again on every
           retry because an export is cut off after 60 seconds. What should this ticket deliver?
Option 1:  "Exports of any size finish (Recommended)". Staff stop seeing the failure. About 1 to
           1.5 days of agent time and 1 to 2 hours of yours; no Retry button.
Option 2:  "Retry button, as written". Staff can try again, but lists above 50,000 rows still
           fail every time. About 2 to 3 hours of agent time.
Option 3:  "Both". Option 1, plus a Retry button for the rare other failure. About 1.5 to 2 days.
```

**Questions to ask here**

- Is this required, or optional? Can it be simpler for now?
- Does something we already have solve this, such as the same worker on a different queue?
- What does this add on top of what exists?
- Can we skip it until we know we need it?
- What is the strongest objection? Did you answer it, or wave at it?
- What does it cost per month?
- Is good enough for use enough here?
- High value, says who? How confident?
