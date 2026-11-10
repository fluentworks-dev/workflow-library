**Do, in this order**

1. Close your notes. Go through each scenario in the file from the first step to the goal, then through each failure, as the user would, with only the examples in front of you. Where you needed anything more, change the use, not the help text.
2. Keyboard only: every control is reachable in reading order, focus is always visible, a dialog keeps focus inside and gives it back when it closes. Then at 200 percent zoom.
3. Render at a phone width and a desktop width, in light and in dark when the product has both. Take screenshots for your own review; do not upload them.
4. Place each new surface beside its neighbours: the same spacing, the same components, the same words.
5. Measure: the contrast of every new pair of colors; the longest real text and half again as long; zero, one and many rows; every graphic at the smallest size a user meets it. Open every state; a state never looked at is not designed.
6. Go through the four lenses below. For each line write the finding, or "holds" with what you looked at. "Holds" with nothing behind it is not a review.
7. Reread every string in the file and in your notes against the final design. Fix what no longer matches.
8. Open the file the way the app shows it, not straight from disk: through a host page that holds only `<iframe sandbox="allow-scripts allow-popups allow-popups-to-escape-sandbox" src="...">` pointing at the file. From disk the file can do things the app's panel forbids, so a check from disk passes on a file whose pages are empty in the app. Use the widths a reviewer uses: about 880 pixels (the app's artifact panel in a 1440 pixel window) and 390. Open every tab and every state there. Measure, on every surface: no script error; each page shows its content at its full height (count the text in it; a frame that exists can still be empty); each page renders at a scale of 1; each change in After has its numbered outline; there are at most two control groups; no file, class or token name appears in the text. On the first tab, every step opens its surface. Fix any that fails before anything else.
9. Fix what you found, in the file. What you could not resolve becomes a question for the Software Design Review, written with the options you see.

Then count again: actions, decisions, fields and the concepts the user must hold, before and after. If no count went down and nothing else is plainly easier, the design is not finished.

**Lens 1: Nielsen's ten usability heuristics**, each with its test and the rules that serve it

1. Visibility of system status (a result per item; "finished" apart from "error"). After every action, is there a visible result within a second, where the user was looking?
2. Match between the system and the real world (the user's word; exact numbers with units). Read each label aloud to someone who has never seen the code: does it name a thing they know?
3. User control and freedom (reversible first; never lose what was typed). Can the user leave any dialog and any flow of several steps without losing earlier work?
4. Consistency and standards (one name per concept; the design system). Beside its two nearest neighbours, what differs without a reason?
5. Error prevention (lock instead of reject; refuse a file when it is chosen). For each error message, could the design have prevented the error?
6. Recognition rather than recall (unknown values shown; locked fields shown with their reason). Can the user finish without leaving the surface to look something up?
7. Flexibility and efficiency of use (safe defaults; one question). How many actions does the tenth use take?
8. Aesthetic and minimalist design (cut steps; no decoration). Remove each element in turn: is it missed? If not, it goes.
9. Help users recognize, diagnose and recover from errors (the system's sentence for what the user can fix; no internals). Does each message say what happened, whether the work was kept, and what to do now?
10. Help and documentation (a named human; help in the same place). Where does a stuck user go, and is that the same place on every surface?

Report one row per heuristic:

| Heuristic | Finding | Fix |
|---|---|---|
| 1 Visibility of system status | The upload row shows nothing between "chosen" and "done" | A bar with the percent on each row |
| 3 User control and freedom | Holds: every row has Remove until it finishes; the dialog closes on Escape | None |
| 5 Error prevention | A file over the limit fails after minutes of uploading | Refuse it when chosen, with the limit and what to do |

**Lens 2: accessibility, WCAG 2.2 level AA** (the criterion in brackets)

- Text contrast at least 4.5 to 1, and 3 to 1 for large text: 24 pixels and up, or 18.66 pixels bold and up (1.4.3). Measure it; record the ratio for each new pair, in light and in dark.
- Controls, focus rings, icons and chart marks that carry meaning: at least 3 to 1 against what is next to them (1.4.11).
- Color is never the only carrier of meaning (1.4.1).
- Everything works by keyboard alone, focus can always leave, and the order follows the reading order (2.1.1, 2.1.2, 2.4.3).
- Focus is always visible and never hidden behind a sticky header, a footer or an overlay (2.4.7, 2.4.11).
- Targets at least 24 by 24 CSS pixels, or spaced so that 24 pixel circles do not overlap; 40 or more for primary and touch targets (2.5.8).
- Whatever is done by dragging can also be done by single presses (2.5.7).
- Every control exposes a name, a role and its state, and the name contains the visible label. Native elements first; ARIA only for what they cannot express (4.1.2, 1.3.1, 2.5.3).
- Every input has a visible label, and the format is stated before the user types (3.3.2, 2.4.6).
- A field in error is identified in text, with a suggestion when one is known (3.3.1, 3.3.3).
- A submission that deletes, pays or binds legally is reversible, reviewed before sending, or confirmed (3.3.4).
- Status messages ("Saved", "3 results", an error summary) are announced without moving focus (4.1.3).
- No sideways scrolling at 320 CSS pixels wide; text resizes to 200 percent; wider text spacing does not clip (1.4.10, 1.4.4, 1.4.12).
- Content shown on hover or focus can be dismissed, can be hovered, and stays until dismissed (1.4.13).
- Informative images and icons have a text alternative; decorative ones are hidden from assistive technology (1.1.1).
- A time limit can be extended; a session about to expire warns first and keeps the user's input (2.2.1).
- Help sits in the same place on every surface (3.2.6). Nothing is asked twice in one flow (3.3.7). Sign-in has no memory test without an alternative, and paste and password managers work (3.3.8).
- Nothing flashes more than three times a second (2.3.1); the reduced-motion setting is honored.
- Dialogs: focus moves in on open, stays inside, and returns to the control that opened it; Escape closes.
- Output without a screen: no meaning by terminal color alone; an email reads correctly as plain text; link text says where it goes.

**Lens 3: layout**

Squint: the heading, the main content and the primary action are the three things you see. Everything sits on the spacing scale and shares edges. Nothing is a raw value. If the new surface looks better than its neighbours, the difference belongs in the design system for all of them, or it is taste and comes out.

**Lens 4: where designs commonly drift**

- Words that disagree with the product. It is the finding reviewers raise most. Reread, and treat a finding about wording as a real finding.
- Behavior that flips while being built: what "archive" means changing again and again inside one change. Settle the behavior and its name here, where a change is cheap.
- Names changed after they were in use, or one thing spelled two ways. One terms list; one spelling and casing per concept.
- Typos and placeholders frozen into things that last. Proofread; read twice any string that will be stored or matched on.
- One-off and internal pages built below the bar: a page assembled by joining strings, a bare failure, no labels. Do not sweep old ones; do not design new ones that way.
- What a surface reveals, designed with care and reviewed rarely: whether a thing exists, a sensitive value in a message or a log, an internal identifier, a token in the page, a list of users shown to someone not signed in. Write the finding even when the fix belongs to another workflow.
- A known gap that could expose data or lock a user out is never a note in the design. It is a question to the person, with the risk stated.
- An unconfigured environment silently talking to the development one. A surface that depends on an environment shows which one it is talking to and refuses to start without that value.
- Proof that does not bite: a mock styled by hand, example data that is always short and valid.
- Tests that do not run on a pull request, so a sentence changes and nothing notices. Name in the hand-off the sentences and states that need a test which runs every time.
- A finding left unanswered. Every finding gets an answer: fixed, with where; or declined, with a fact you verified.
- A large change approved unread. Make the file easy to review: the list of surfaces, a label on every change, and in your summary the three things most worth the person's attention.
- Accessibility added only when a reviewer asks. Names, focus order and contrast are in the first draft and in the specification as requirements.
- Alerts addressed to one human. Notifications go to a team address or channel, named in the design.
- Rules applied to new work and not to old. Apply them to what this ticket adds or changes; what you noticed elsewhere is fixed when small, and otherwise one line for a new ticket.

**Template: findings from a real run.** When the surface is a tool or a platform and someone ran it on a machine unlike yours: most severe first, each change tied to something that happened, then what was considered and left.

```
| Severity | What bit | Change |
| P0 | Install stopped on Windows: the folder name had a space and the script did not quote it | Quote every path |
| P1 | "401" shown with no explanation after a mistyped key | "That key was not accepted. Copy it again from Settings, Keys." |
| P2 | The progress line repeated 40 times in a narrow terminal | One line, updated in place |

Considered but not changed
- Ask for the region: every user so far wanted the default, and a second question is a place to stop.
```

**Template: verification record** (it becomes the last section of `product-design.md`)

```
Verified
- Walked the upload of 5 files with 2 failing, at 375 and 1440 pixels wide, light and dark:
  no overflow; the summary stays visible while the list scrolls
- Keyboard only: Choose files, each Remove, Retry failed and Upload reachable in order; focus
  returns to Choose files after the last Remove
- Contrast: text-danger on surface-raised 5.9 to 1 (light), 6.4 to 1 (dark); progress bar on
  its track 3.4 to 1
- Actions for 5 files: 11 before, 3 after
Not verified
- [ ] A screen reader announces the summary once: needs a real device. Steps: open the file with
      VoiceOver on, choose two files over the limit, press Upload; "3 of 5 files uploaded. 2 need
      attention." is read once and focus does not move
Findings fixed
- Visibility of system status: nothing between "chosen" and "done". Fix: a bar with the percent on each row
- Target size: Remove was 16 pixels. Fix: a 24 pixel target, 40 at narrow widths
For the Software Design Review
- Should a refused file stay in the list until removed (default), or leave after 10 seconds?
  The default keeps the reason visible; the alternative keeps the list short. The 10 seconds
  is chosen by feel.
```

**Questions to ask here**

- Did you open it the way the app shows it, or does a reader see an empty screen?
- Did you actually open it in dark mode?
- What is the contrast? The number.
- Does it work with only a keyboard?
- Does this work on a phone as well?
- How does it look next to the page beside it? Same spacing?
- Is this still true? You changed the design after you wrote this line.
- How many actions now?
- What did you not test? Leave the box unchecked.
