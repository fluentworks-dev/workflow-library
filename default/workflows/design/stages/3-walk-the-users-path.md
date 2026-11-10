**Do, in this order**

1. For each scenario from the previous stage, the most used first, write the user's goal in one sentence.
2. Write today's path as numbered steps, exactly as you went through it. Count the actions, the decisions and the fields.
3. Write the path after this ticket. For each step ask: can it be removed, inferred, given a safe default, merged, moved later? Cut until nothing else can go. Count again.
4. Mark the step that cannot be undone. Put the reversible steps before it, and write what the user is told before it happens: what, how much, what it costs.
5. Compare the path with the model in the user's head. Is each thing called what they call it? Does each surface say where they are and what just happened? Can they recover from a mistake at every step? Is anything asked twice?
6. Shorten the tenth use as well as the first: defaults remembered, bulk actions, the keyboard.

**Practice for cutting a path**

- Hick's law: fewer and simpler choices decide faster. Mark the recommended option and keep rare ones out of the main path.
- Tesler's law: complexity that cannot be removed is carried by the system, not by the user.
- Jakob's law: use the convention users already know from other products before inventing one.
- Progressive disclosure: show what the common case needs and reveal the rest on request. Never hide cost, what is published or what cannot be undone.
- Undo beats confirmation for reversible actions: a confirmation on every action trains users to click through.
- Fitts's law: the next action is large and near where attention already is; destructive actions sit apart from frequent ones.

**Decide, and ask only what is the person's**

- Decide placement, layout, component and wording yourself. Where real alternatives exist, note them for the switch in the file. Do not ask.
- Put a point in the session's batch (asked at the start of "Hand off") only when the alternatives differ in what a user can do, never in where something sits or how it reads. Never ask from a written description: take one simple screenshot per option (only the part that differs, labeled A, B, C), upload each as a preview artifact, name the screen, link each screenshot in its option, and put your recommendation first.
- A decision in `scope.md` is reopened at most once, when the path shows a concrete thing the user would meet that makes it wrong. Say that thing in the user's words, not in terms of locks or bindings.
- A question about how data is loaded, stored or detected belongs to System Design or Code Design. Record it. Do not ask it, and do not answer it by designing a new stored status or a schema change.
- When the product's rule for changing something is undecided, design the surface that shows it, hold the one that changes it, and say so in one sentence.

**Template: path table**, here for the goal "A project member gets five result files into the project and knows each one arrived":

| Step | Today | After | Why it goes or stays |
|---|---|---|---|
| 1 | Open Upload files | Open Upload files | Stays |
| 2 | Choose one file | Choose or drop all five | Merged: the picker takes many |
| 3 | Press Upload | Press Upload, once | Stays: it is the point of no return for the batch |
| 4 | Wait, with nothing shown | Each row shows its progress | Status visible |
| 5 | Repeat steps 2 to 4 four more times | Gone | Removed |
| 6 | On failure, guess which file and why | Failed rows say why; one "Retry failed" | Recovery in one action |

Count: 11 actions before, 3 after.

**Template: a question to the person.** The question text holds the situation and the question. Each option holds its own consequence and its screenshot, the recommended one first. Nothing is said twice: no option, argument or link appears in the question text as well.

```
Question:  On Upload files, a second file with the same name replaces the first without a word,
           and a replaced file cannot be brought back. What should happen when the name is
           already in the project?
Option A:  "Refuse the file (Recommended)". The user is told to rename it or remove the existing
           file first. Nobody loses a file; a user who meant to replace has one more step.
           [screenshot A]
Option B:  "Ask before replacing". The user picks Replace or Keep both. A replace still cannot
           be undone, and the kept copy needs a naming rule. [screenshot B]
```

**Questions to ask here**

- What is the user trying to get done? One sentence.
- Why does the user have to do this step? Can we do it for them?
- Is this required, or optional? Can it be simpler for now?
- Can we infer it? Can it default to something safe?
- What happens if they do nothing?
- Which step cannot be undone? Do they know before they press it?
- Does the service already enforce this? Then why is it in the form?
- Only display, not create, until the product decides?
- How many actions before? How many after?
