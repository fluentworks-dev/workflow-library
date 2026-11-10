Design the use before any surface. Here you settle who reaches for this, in which situation, and what using it looks like from wanting something to having it. The surfaces come in the next stages. This holds alike for a screen, a command, a call a developer writes, a setting an operator edits and a document someone follows.

**Do, in this order**

1. **Write the promise.** One sentence the user could say to a colleague, in their words, with no mechanism in it: "Pick a day, and the report is in my inbox every week, with no spreadsheet to keep up." State the problem the same way, as someone meets it: "Nobody can answer 'where does this property come from' or 'which systems hold this' without reading pipeline code."
2. **Name each user and their situation.** Take the user from `scope.md` and add what a design needs: what they are in the middle of when they reach for this, how often, what they already know, what they have at hand. Name them the way a change names its consumer: "to support the orders page", "for backfill jobs".
3. **Write the scenarios.** Two to four real situations with real values: the usual one, the first time, the one where it goes wrong, and the one at the size that hurts (none, one, five hundred). A design says how it behaves in different scenarios.
4. **Show what using it looks like, before explaining anything.** For each scenario write the use itself, numbered: what the user opens, types and presses, and what comes back; the exact command and its output; the call as the caller writes it; the lines someone adds to a configuration file. If writing a schema needs more than the examples in the design, the design has failed: when a user needs more than these examples to use it, change the use; do not add help text.
5. **List what the user must know.** Every concept they have to hold to use this, in their word for it, with what it is. Then remove: a concept that can be derived, merged into one they already know, or left for later comes out. A fact has one home: the user states a thing once, and what the product can work out it never asks. Two things that mean different things stay two, however alike they look.
6. **Decide where it lives.** Start from the perfect product, not from what exists: in a perfect product, how would the user interact with the concept, and where should it live? Then look at what the product has, to see whether that place exists or a new one is needed. Reuse an existing surface, command or endpoint whenever it is that place; reuse scales better. A page that is only the nearest one is not that place, because a page keeps to its one concern: an integrations page in settings is about connecting outside services, so a new concept such as a library is separate and gets its own place. Below the surface the order is the same: a capability the platform or the technology already has before one built for this. Before adding a feature, check whether an existing one, used differently, already does it, such as the same worker on a different queue.
7. **Decide what the first version covers, and say that it is narrow.** Inside what `scope.md` allows: one kind of thing from end to end before every kind. Say it plainly, for example: for now it works only for one kind of report, and it will be generalized once more kinds are needed. What is left out is a seam: write what the user meets in its place, so that nobody builds around it.
8. **Give the size it serves a number.** For example, a hard limit of 1,000 items per project is a safe practical ceiling for this system. Design for that number, and say what the user sees past it.

**Decide, and ask only what is the person's**

- Yours: the scenarios, the concepts and their names, where it lives, the defaults, and how much of what the scope allows the first version covers. Record each choice that is not obvious under "Decided for you".
- When two ways of using it are both real, build the one you recommend and keep the other as an alternative in the file. Before you commit to your recommendation, state the strongest objection to it.
- When only trying can settle the choice, build both small and try them, then keep the simpler one.
- Ask the person only when the two ways differ in what a user can do, in the way the next stage describes.

**Template: the use** (it opens `product-design.md`)

```
Promise:        "I pick all my result files once and see which ones arrived."
Users:          A project member at the end of a test run: 5 to 20 files, a few times a week.
                An engineer's nightly script that nobody watches: the `upload` command.
Scenarios:      1 Five files, all arrive.
                2 Five files; one is over the limit and one loses its connection.
                3 The nightly script; one file fails.
                4 The first upload into an empty project.
Lives in:       The existing Upload files page and the existing `upload` command. No new page.
First version:  Files up to 5 GiB each, up to 100 at once. Resuming an interrupted upload is left
                out: a failed file starts again from the beginning, and its row says so.
```

**Template: what using it looks like**

```
Scenario 2, on the page
1. Open Upload files. Drop the five files on it.
2. results-3.csv is refused at once: "results-3.csv is 6.2 GiB. The limit is 5 GiB per file.
   Split it and upload the parts."
3. Press Upload. The other four rows show their progress.
4. results-5.csv fails: "The connection dropped. Your other files are safe." Press Retry failed.
5. "4 files uploaded to Field study 2." The refused file is still listed, with its reason.

Scenario 3, in the script
$ tool upload results-*.csv
failed    results-3.csv  6.2 GiB is over the 5 GiB limit per file. Split it and upload the parts.
2 of 3 files uploaded. 1 failed.
$ echo $?
1
```

**Template: what the user must know**

| Concept | The user's word | What it is | Stays or goes |
|---|---|---|---|
| File | file | One thing the user uploads | Stays |
| State of a file | uploading, uploaded, failed | Where one file is, shown on its row | Stays: three values, each with its word |
| Limit | "5 GiB per file" | The largest file accepted | Stays: one number, told before it is hit |
| Batch | none | The files chosen together | Goes: the user never names it; the summary says "5 files" |
| Upload session | none | What the service uses to follow a transfer | Goes: the product derives it and never shows it |

Count: 5 concepts in the first draft, 3 after.

**Questions to ask here**

- Who is this for? What do they say when they ask for it?
- What does it look like when someone uses it? Show it, don't explain it.
- Think from the point of view of whoever uses the data.
- How many things does the user need to know to use this? Can one go?
- Why does the user have to tell us this? Don't we already know it?
- In a perfect product, where would the user look for this? Does that place exist already?
- Is there already a place for this? Can it go inside something already deployed?
- Can we fix it without adding new machinery?
- Can a feature be skipped until we know we need it?
- Is this the smallest version that is good enough for use? What did we leave out, and what does the user see there?
- What is the number? Where does it come from?
- What happens in the scenario where it fails?
