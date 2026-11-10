Read, in this order, and note the source of each fact:

1. The ticket and every comment on it.
2. The description of the project and its stated direction.
3. Every document already on the ticket. An earlier scope.md is built on, not restarted.
4. The questions earlier sessions asked and the answers given. An answered question is settled. A question closed without an answer may be asked once more.

When the project names a repository, clone it whole, with its branches (no shallow clone, no single-branch clone), and read the repository's guidance file and its README. When the project names one repository and the ticket names none, that is the repository. Never add one that nobody named.

Look into the code only far enough to see where the change would land, what it would touch and how big it is: no survey of every call site and no proposed changes. When you can, run the product or its preview and go through today's path once yourself.

Write down, in the person's words where you can: **who** has the problem and at what moment; **what** they cannot do today, and how often; **the proposal** the ticket makes, kept apart from the problem; **the consumer** who asked, and what for. The form "when [situation], I want to [motivation], so I can [result]" separates the need from the feature requested.

Check each fact the ticket asserts before you rely on it:

| The ticket says | Check before scoping |
|---|---|
| "People cannot do X" | Do it yourself, or read the path in the code. Often X is possible and hard to find, which is a different and smaller problem |
| "It fails" or "it is slow" | Reproduce it or read the logs. Record how often, for whom and since when |
| "Show the count, the time, the duration" | Confirm in the code that the data is recorded today. What is missing is an open question for System Design, never an assumption |
| "Nothing uses this" | Search every consumer you can reach, and name each caller found |
| "It is a small change" | Open the code far enough to see where it lands |
| "Users want this" | Find who said it, and what they were trying to do |

When the ticket comes from findings in real use, list them by severity (P0, P1, P2), each traced to something that actually failed during that use, and scope the round from that list only.

Compare the ticket with the default branch and with its project. When another ticket already delivered part of it, or the project has moved on, say so now: the ticket is revised in place with the person's approval, never silently reinterpreted.

Decide whether there is scoping to do. There is none when an earlier scope.md settles everything and nothing has changed since, or when the ticket is a fully specified change with one reading, a named repository, an observable outcome and no product choice in it. Then go straight to "Write scope.md" and write the three-line form.

Leave this stage with working notes in this form:

```
Who: staff who export order lists from the orders page.
What: an export of a large list ends in "Export failed"; they split the list by hand.
Proposal: add a Retry button to the failure message.
Consumer: the operations team (ticket author), for month-end reporting.
Checked: reproduced with a 60,000-row list in the preview: fails after 60 s.
Checked: request log, last 30 days: 41 failed exports.
Unknown: why it fails. The ticket asks for a patch: trace the cause next.
```

**Questions to ask here**

- What is the point of this? Who asked, and what were they trying to do?
- Is this the problem, or the fix someone already picked?
- Can they really not do it today? Did you try?
- How often? 3 times a year or 40 times a month?
- Is that data even recorded?
- Did an earlier session already ask this? What was the answer?
- Which repository? Did someone name it, or did you guess?
