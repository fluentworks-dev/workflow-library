**Do, in this order**

1. For each surface on the path, fill the state table. Where a state cannot occur, write "cannot occur" and the reason. Leave no row blank.
2. Write the exact wording of every string in every state: headings, labels, buttons, help text, messages, the text of an email, each line of command-line output.
3. Fill the error table: every failure the path can hit, who can act on it, where it is shown, the exact wording, and what the user does next. Sort each failure first: the user can act (show the system's sentence unchanged); saying more would reveal that something exists (one sentence for both cases); the system failed (the general sentence and a request id).
4. List the terms: one name per concept, and the words not to use.
5. Give every limit, freshness and wait a number and a unit. Mark the ones chosen by feel.
6. For every form, write one specification block per field.
7. For output without a screen, write the exact text before and after, including the output on failure and the exit code.
8. List every graphic a surface needs (an icon, an illustration, a diagram, a chart, an image, a brand mark), with what it tells the user and where it comes from. A graphic that tells the user nothing comes out.

**Decide yourself:** every wording, every empty state, every default, the order of fields, which component, which graphic. Record each choice that is not obvious under "Decided for you". **Do not decide** whether the product allows an action at all: ask, or hold the write path.

**What each state must tell the user**

- Empty at first use: what belongs here and the one action that starts it. Empty with no results: that nothing matched and how to widen it; never the first-use screen.
- Loading: that something is coming and its rough shape. Partial: which parts arrived and which did not.
- Too many: that there is more, how many, and how to reach the rest. Too long: the full value stays reachable.
- Error: what failed, whether their work was kept, what to do next. Disabled or locked: why, and what would enable it.
- No permission: the same as "does not exist" whenever saying more would reveal that it exists. Stale: how old the data is.
- Success: what happened and what changed, in the place they are looking.

**Template: state table, one per surface**

| State | What the user sees | Exact wording |
|---|---|---|
| Empty, first use | A drop area with one button and the limit | "Drop files here, or" [Choose files]. "Up to 5 GiB per file." |
| Empty, no results | Cannot occur: the surface has no search | |
| Loading | A row per file with a bar | "results-1.csv, 40 percent" |
| Partial | Finished and failed rows marked; a summary above | "3 of 5 files uploaded. 2 need attention." |
| Too many | Rows scroll inside the list; the count stays visible | "128 files" |
| Too long | The file name truncates in the middle; the full name shows on focus and hover | |
| Error | The failed row with its reason and Retry; [Retry failed] above the list | See the error table |
| Disabled | [Upload] is disabled only while nothing is chosen, with the hint under it | "Choose at least one file." |
| No permission | The page is replaced by the not-found message | "This item does not exist or you do not have access to it." |
| Stale | Cannot occur: the surface shows no fetched data | |
| Success | The summary, and each row marked done | "5 files uploaded to Field study 2." |

**Template: error table**

| Failure | Who can act | Shown where | Exact wording | Next action |
|---|---|---|---|---|
| File over the limit | User | On the row, when the file is chosen | "results-3.csv is 6.2 GiB. The limit is 5 GiB per file. Split it and upload the parts." | Remove |
| Name already in the project | User | On the row | The service's sentence, unchanged | Rename and retry |
| Connection dropped | User | On the row | "The connection dropped. Your other files are safe." | Retry |
| The service failed | Team | On the row | "The server ran into a problem. Try again, and contact support if it keeps happening." Request id below | Retry |

**Templates: messages**

```
Error         Heading: the class of failure. Body: what happened; whether their work was kept;
              what to do now. Action: the fix, or the human to contact. Id: only for a system failure.
  Could not save / Someone else changed this project while you were editing. Your changes are
  still in the form. Refresh to see theirs, then save again. / [Refresh]
Empty state   Title: what belongs here. Body: why it is empty. Action: the one that starts it.
  No files yet / Files you upload to this project appear here. / [Upload files]
Confirmation  Title: the question, with the real name. Body: exactly what is removed, sent or
              charged, with counts; that it cannot be undone. Confirm: verb and object. Cancel: focused.
  Delete "Field study 2"? / This removes the project, its 12 runs and 48 files for
  everyone. This cannot be undone. / [Delete project] [Cancel]
Success       What happened, to what, in the past tense; the next step when there is one.
  5 files uploaded to Field study 2.
Cost          Before anything that bills: what is created, what it costs, from when, how to stop.
  This creates one small server and one database, about USD 40 per month, starting today.
  Delete the app to stop the charge. / [Create] [Cancel]
Publish       Before anything leaves the product: in plain language exactly what would be
              published, to whom, and that it cannot be taken back.
  This publishes the folder "floor-plan" (212 files) to a new private repository that only
  you can see. Nothing in it is public. / [Publish] [Cancel]
```

**Pairs: the register to write in**

| Instead of | Write |
|---|---|
| "Error 500: KeyError 'item_id'" | "The server ran into a problem. Try again, and contact support if it keeps happening." and the request id |
| "Forbidden" on a record the user may not read | "This item does not exist or you do not have access to it." |
| "Invalid value" | The rule and the value: "Size must be a whole number from 1 to 100. You entered 250." |
| "Upload failed" | "2 of 5 files were not uploaded:" then each file with its reason |

**Templates: output without a screen**

```
Email         Subject: what happened and to what. First line: the outcome. Body: only what the
              reader can act on. One action. Why they received it and how to stop it.
  Subject: Upload finished: 3 of 5 files in Field study 2
  Two files were not uploaded: results-3.csv is over the 5 GiB limit; results-5.csv lost its
  connection. [Open the upload] You receive this because you started the upload.
Tool error    For a user who cannot debug: domain, message, remedy. No stack trace.
  Naming / The name 'X' is already taken by another app. / Pick a different app name.
Onboarding    Each step: what it does; how it knows it is already done, which counts as success;
              what each result means; what the user sees on failure; the human to contact.
  Register the key. Already registered: continue. Probe: "401 means a bad key; any other
  code, for example 406, means the key was accepted". On failure: "That key was not accepted. Copy it
  again from Settings, Keys." Still stuck: the platform team's channel.
Release notes For operators: what changes at the machine, in their words. For developers: what
              changed in the code. Breaking changes: the exact configuration text to paste.
Runbook       Each step: the exact command, the expected result, the condition that proves it
              worked. Then a table from symptoms to causes.
  Symptom: the sign-in page reloads forever, nothing in the logs. Cause: the session is too
  large to store. Recovery: the exact command, then sign in again; the page loads once.
```

**Template: one field of a form**

```
Field:       Association name (optional)
Control:     text input with suggestion chips, labeled "Suggestions"; a typed value is accepted
Blank:       the key is left out of the request; the service applies its default
On submit:   no client rule beyond the published maximum length (80)
Error:       the service's sentence under the field, kept until the next submit
Locked:      after the file is attached: grayed, "cannot be changed"
```

**Template: terms**

```
file: one thing the user uploads. Never: asset, document, attachment.
upload: sending a file into a project. Never: import, ingest.
```

**Practice lists, by the kind of surface you are writing**

Forms:

- One column. Labels above their fields and always visible; a placeholder is never the label.
- Ask only for what the task needs now. Mark the few optional fields "(optional)" instead of starring the rest.
- Order and group fields as the user thinks of them. Size a field to the content it expects.
- Two to five options as radio buttons, more as a select or a searchable list; a switch for what takes effect at once, a checkbox for what is submitted with the form.
- Accept input liberally and normalize it (Postel's law): spaces in a code, upper or lower case, a pasted line break. Refuse only what cannot be understood.
- State the format before the user types. Set the input type and the autocomplete hint.
- Put the error beside its field in words, move focus to the first error or to a summary that links to each, and keep every other value.
- The primary button names the action and sits where the form ends. Do not disable it without a visible reason.

Content and wording:

- Lead with what matters to the user. One idea per sentence. Active voice, present tense, short common words.
- Sentence case for headings, buttons and labels. No full stop on a label or a button.
- A button is a verb and its object ("Archive project"), never "OK" or "Submit" where a specific verb fits. A dialog's title asks the question its button answers.
- A link says where it goes; never "click here".
- No blame and no long apology. Leave out "please", "simply", "just", "easy", exclamation marks, and jokes in errors.
- Dates that cannot be misread ("4 Nov 2026"). Relative time for the recent past, with the exact time within reach.
- Write for translation: no sentence assembled from fragments, and room for text half again as long.
- Define a term the first time it appears, and then never vary it.

Feedback and waiting:

- Response-time limits (Nielsen): up to 0.1 second, no indicator; up to 1 second, at most a subtle change; beyond 1 second, show that work is under way; beyond 10 seconds, show progress, an estimate, or a way to leave and come back.
- A skeleton in the shape of the content for a page; a spinner only for a short wait in a small place; a bar with a count when the total is known.
- Update optimistically only when failure is rare and cheap, and design the rollback and its sentence.
- A toast confirms something reversible. An error the user must act on stays until dismissed.
- Every action gets a visible result where it was taken: the button changes, the row appears, the field marks itself saved.

Command-line output:

- Results go to standard output; progress and diagnostics go to standard error.
- Exit 0 on success and non-zero on any failure, so that a script can rely on it.
- `--help` with one example on every command; a usage line for a missing argument.
- Print what is about to happen before doing it; `--dry-run` for anything that changes state.
- Readable by a human by default, with a flag such as `--json` for programs. Never color as the only signal; honor `NO_COLOR` and a terminal that is not interactive.
- On failure: what failed, what to do, where the full log is. A stack trace only behind a verbose flag.
- On success, name the obvious next command. Align columns, keep one fact per line, and keep the words stable between releases: someone is matching on them.
- A build target that needs credentials stops at once and names them (`$(error ...)` in a Makefile). An entry point carries its full invocation at the top of the file.

Emails and notifications:

- The subject states what happened and to what: "Upload finished: 5 files in Field study 2". The first line repeats the outcome. One primary action.
- The plain-text version reads correctly without images.
- Send only what the reader can act on or needs to know; batch the rest. Say why they received it and how to stop it.
- Never a secret, a token or a sensitive value; link into the product instead.
- It makes sense to someone who has not opened the product today: the thing, its state, the next step.

Graphics:

- A graphic earns its place by saying something faster than words can: a state, a kind, an amount, a relation, a place. Decoration comes out.
- Icons come from the product's set, at its sizes, with one meaning each across the product. An icon stands alone only when it is universal (close, search); otherwise its label is beside it. It always has an accessible name, and it is never the only sign of a state.
- An illustration appears only where the product already uses them, in its style, and never in an error.
- A diagram holds one idea. Its boxes are things the user knows by name, its arrows say what moves, and it reads in one direction. Past about seven boxes, split it. Its text is real text.
- A chart answers one question, and its title says which. Axes carry units, bars start at zero, and a few series are labeled on the chart instead of in a legend. Color carries meaning, and the numbers stay reachable as text.
- An image or a screenshot shows the real thing at the size it is used, with a text alternative, in a space reserved for it.
- A logo, an application icon or a favicon is generated from the product's source file. Never redraw or recolor one by hand; keep the clear space and the smallest size its guide gives.
- Every graphic can be read at the smallest size a user meets it: check the smallest text and the thinnest line there, in light and in dark when the product has both. The parts that carry meaning keep a contrast of at least 3 to 1.

**Template: one graphic**

```
Graphic:   the mark on a file row that failed
Tells:     this file did not arrive; it sits beside the word "failed"
Source:    the product's icon set, at 16 pixels, in text-danger
Name:      "Failed"
```

**Questions to ask here**

- What does the user see when there is nothing? When there are 500?
- What does it say when it fails? Can they do anything about it?
- Which file failed? Why? Say it.
- Is the user's work kept?
- Are we showing them a stack trace? Don't.
- Does this message tell someone the item exists when they have no access?
- Same word everywhere? What do we call this thing?
- What is the number? What is the unit? Where does it come from?
- Unknown value: show it or hide it? Show it.
- Does the script still exit 0 when something failed?
- What does this icon tell the user that the word beside it does not?
- Is it from our set, or a new one?
