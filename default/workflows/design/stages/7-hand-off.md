**Do**

1. **Ask now, once.** Every point earlier stages left for the person goes in one AskUserQuestion batch, each option with its screenshot, the recommended one first. Apply the answers to the file and the document before you attach or save anything. When nothing is the person's, ask nothing.
2. When a file was built, attach the one HTML file to the ticket as its design artifact, named after the ticket, for example "PRJ-41 product design". Do not upload separate current and new files. A revised file replaces the old one; never add another. When the design is text only, the status line says "Design file: none, text only" and nothing is uploaded.
3. Upload no screenshots except the ones for each option in a question to the person.
4. Save `product-design.md` on the ticket, always under that name, with the sections below in this order. Keep a section with "None" instead of dropping it. A revision edits the copy that reading the document saved and is saved from that path, with a status line saying what it replaces; it is written from scratch only when its concepts change.
5. Do not ask the person to approve it. It records decisions they made and choices they can overturn.
6. Write no tasks, no plan and no code structure. Code Design decides how to build it. State what the design needs from the system as a need, never as a mechanism.
7. When the repository's guidance says a design system change or a changed screen must be republished or redrawn somewhere after it merges, list that step in the document so that the merge does it.
8. Summary: one line saying what the user can now do more easily, any new tokens or components, and a link to the file. Report your status with the progress tool when the session provides one.

**Before saving, confirm the document holds**

- The promise, each user and scenario with what using it looks like, and the concepts the user must hold.
- The user's goal and their path, with the count before and after.
- Every surface with every state and the exact wording of every string. "Something like" is not a specification: wording is copied into code and into tests as written.
- The existing tokens, components and graphics to use, by name; what is new, and why an existing piece would not do.
- Each alternative behind a switch, the default, and why.
- What the design deliberately does not do and what it must never do, each a sentence a reviewer can verify.
- The acceptance in observable terms: the user can do this in so many actions; this named invalid input shows this named sentence.
- Where a reason that must outlive the ticket will live: the design system's documentation, or a comment where the choice is made.
- What you verified and what you could not.

It never holds tasks, files to create, code structure, or storage and schema choices.

**Template: product-design.md**

```
# Product design: <ticket key> <title>
Status: <draft, or "revision N, replaces revision N-1 because ...">
Design file: <the name of the attached file>

## Use
<the promise; each user and their situation; the scenarios; what using it looks like, numbered;
 the concepts the user must hold; where it lives; what the first version covers and leaves out>
## Goal and path
<the goal in one sentence; the path table; the count before and after>
## Surfaces
<the inventory table; then for each surface: the state table with exact wording, the layout in
 reading order, the components and tokens by name, the accessible names and the focus order>
## Errors
<the error table>
## Output without a screen
<the exact text before and after, or "None">
## Terms
<one name per concept; the words not to use>
## Design system
<the existing tokens, components and graphics used; for each new one: its name, its role, why the nearest
 existing piece would not do, the surfaces that use it>
## Alternatives in the file
<each switch: the options, the default, why>
## Not in this design
<write paths held; states out of scope; seams for a later ticket; what changes for a consumer, in bold>
## Decided for you
<one line per choice made without asking>
## Decisions
| Decision | Choice | Reason | Decided by |
<each answer the person gave, in their terms, with its date>
## Also fixed here
<small things found on the way and included>
## For a new ticket
<larger things found on the way: surface, what is wrong, rough size>
## Open questions
<for System Design; for Code Design; for the Software Design Review>
## Verified
<the verification record>
```

Filled, abridged:

```
# Product design: PRJ-41 Upload several files and show which ones failed
Status: draft
Design file: PRJ-41 product design

## Use
"I pick all my result files once and see which ones arrived." For a project member at the end
of a test run, and for an engineer's nightly script. Lives in the existing Upload files page and
`upload` command. First version: up to 5 GiB per file and 100 files at once; no resume.
The user must know three things: a file, its state (uploading, uploaded, failed), the limit.

## Goal and path
A project member gets result files into the project and knows each one arrived.
Five files: 11 actions before, 3 after.
## Design system
Uses Button (primary, quiet), ProgressBar, tokens text-danger, surface-raised, space-2 to space-6.
New: InlineMessage, variant "danger", for a failure tied to one row. Banner would not do: it
speaks for the whole page. Used by: Upload files.
## Alternatives in the file
Failed files: A stay in the list (default); B move to a "Needs attention" panel. A keeps one
place to look.
## Not in this design
Replacing an existing file: not offered; a replace path is left for a later ticket.
Resuming an interrupted upload: a seam. Each row keeps the file's name and size, so Resume can
be added without changing the list.
## Decided for you
- One press uploads every chosen file.
- A refused file stays in the list until removed, so that its reason stays visible.
- The limit is shown as "5 GiB", the unit the service enforces.
## Decisions
| Decision | Choice | Reason | Decided by |
|---|---|---|---|
| A second file with a name already in the project | Refused, not replaced | A replaced file cannot be brought back | The person, 2026-11-04 |
## Also fixed here
- The Remove target on file rows grew from 16 to 24 pixels.
## For a new ticket
- Project list: the empty state and the no-results state are the same screen. About one day.
## Open questions
System Design: the list must tell "never uploaded" from "upload failed" after a reload. What can
the service report?
```

**Template: the summary**

```
A project member can upload several files at once and see which ones failed and why. New in the design system: InlineMessage "danger". PRJ-41 product design
```

**Questions to ask here**

- Can someone build this without asking you anything?
- Is every string written out, exactly?
- What is new in the design system? Is it needed?
- What did you decide for the person? List it.
- What are we deliberately not doing?
- Why is there a task list in here? Remove it.
- One file, replaced, not a second one?
- One line: what can the user do now that was harder before?
