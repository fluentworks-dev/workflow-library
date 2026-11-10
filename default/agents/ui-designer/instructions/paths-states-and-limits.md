Design the path before the screens: the numbered steps from wanting something to having it, with the waits and the failures.

**Cut steps until nothing else can go**

- For each step ask: can it be removed, inferred, given a safe default, merged, moved later? Then count what is left.
- Ask only what the system cannot work out, and aim for one question. Every further question is a place where a user stops.
- Choose a default wherever doing nothing is safe, and show it. Never invent a default for a value the user must own.
- When a user leaves something out and gets a bad result, change the default, not the user. Example: when consumers that omit a page size get truncated lists, raise the server default (from 50 to 200, say).
- Fix the cost instead of adding a control that manages it. A throttle shown to users that does not work is removed, and the repeated cost is cached.
- Reuse a surface before adding one.
- When a reduced view is needed, derive it from the full one with the same shape and names, so the user learns one structure and one pipeline serves both.
- Practice: fewer choices decide faster (Hick); the system carries the complexity that cannot be removed (Tesler); known conventions before new ones (Jakob); progressive disclosure, but never hide cost, what is published or what cannot be undone.

**Reversible first**

- Prefer a design that can be reversed: archive before delete, a status before removal. Undo beats a confirmation for reversible actions; confirm only what is destructive, costly or leaves the product, with a button that names the action.

**Never lose the user's work**

- A failed submit returns every value intact. Leaving unsaved input warns or keeps a draft.
- Every step is safe to repeat: a double press, a refresh or a retry never creates two of anything, and a step already done counts as done.
- When two users edit the same thing, decide who wins and tell the other: for concurrent editing, the first save wins and the others are told to refresh. Never overwrite silently.
- When an operation can outlive the user's patience or a client's timeout, return at once with something usable (a name, a link, a status to look up) and let them watch state they can observe.

**Show before letting them change**

- When the product's rule for changing something is undecided, design the surface that shows it, hold the one that changes it, and say so in one sentence: the value is only displayed, not created, until the product rule is decided. Showing is safe to ship; a write path sets a rule that is hard to take back.
- When a field cannot be changed, show it locked with the reason (grayed, with "cannot be changed"). A hidden field makes the user wonder whether the value exists.
- When a value is unknown to the surface, show it as it is instead of hiding it: when the upstream service owns a list of types, render the types this application does not know rather than hiding them. A hidden row reads as missing data.
- Two controls that look alike but mean different things stay separate, with the difference written beside them. Example: a switch that turns uploading off, and an optional job reference that links an upload to a job. A blank reference still uploads, unlinked; the switch off uploads nothing.

**Every state**

Design each state that can occur: empty at first use, empty with no results, loading, partial, full, too many, too long, error, disabled or locked, no permission, stale, success.

- Report partial failure per item: every failed item with its error, and overall success false as soon as one failed (`success = not failed_files`). Never "3 of 5 uploaded" without naming the two.
- One bad part never hides the rest: a list or a composed page that hits one corrupt record shows the rest and marks the broken part. A page whose whole subject is the broken record says so.
- "Finished" and "failed" are different events with different words. A success flag that is always true is a defect.
- Never show a list silently cut short: say that there is more, and order by a stable key so that paging does not skip or duplicate.
- An unsupported request gets an answer, instead of silently matching nothing.
- Refuse rather than truncate, overwrite or guess: raise an error so that someone can take a look, instead of returning something the product does not support yet.

**Numbers**

- A word that depends on time or size carries a number, a unit and a reason the user can find: for example, "running" means a status within the last 5 minutes, and results are cached for 90 seconds. Show "updated 2 minutes ago" where staleness changes what the user does.
- Tell a limit before the user hits it, and at the limit say what to do: a 5 GiB upload cap refuses the file up front, with a message telling the user to split the file.
- A view of recent data falls back to the full history, so a device that has not reported recently still resolves. Never "not found" for something merely old.
- Show the true unit: a megabyte is 1000 * 1000 bytes, and MB is not MiB. Ignore a record dated in the future instead of showing it as the latest.
- When a threshold was chosen by feel, say so: the numbers are a judgment of what is reasonable, not a measurement.
- Practice: Nielsen's response-time limits (0.1 second feels instant, 1 second keeps the flow, 10 seconds needs progress). An error the user must act on is never only a toast.
