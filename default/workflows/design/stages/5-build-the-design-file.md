**Build one file**

- Build the file only when a surface is drawn: a screen, a page, a dialog, a graphic, an email. When every changed surface is text (a sentence, a message, a tool's description, a command's output), the exact wording in `product-design.md` is the design, and no file is built; the hand-off says so.
- One standalone, interactive HTML file shows the product design this ticket is going with: how it is used, then every surface it adds or changes. Never split the work into several files. It opens from disk: no build step, and no network beyond the product's own assets.
- The person reads it in the app's artifact panel, and it must work there. The panel frames the file with `sandbox="allow-scripts allow-popups allow-popups-to-escape-sandbox"`, so the file has no origin of its own: its script cannot reach into a frame the file creates (`iframe.contentDocument` is null), and a storage call throws. A file checked only from disk draws every page there, and in the panel each page is an empty box. So each page is a document of its own, set with `srcdoc` and carrying its own script; it tells the outer file its height and each choice made in it by message. Keep storage calls out, or inside `try`.
- Use the product's real stylesheet, tokens and components, and copy the real markup, so that "before" is the product and not a drawing of it.
- Make the new design work wherever interaction matters: open, type, submit.
- Fill it with example data shaped like the real thing: a long name, a missing value, an unknown value, a failure.
- When the file exists and the ticket also changes a use without a screen (command-line text, a call, a configuration file, a message), show its exact text before and after in the same file, in a fixed-width block; an email is drawn as rendered.

**Make it easy for a person to read**

The reader is a person deciding in a few minutes in the app's artifact panel (about 880 pixels wide in a 1440 pixel window, 720 in a 1280 one) or on a phone. Each rule below answers a common fault in before-and-after design files: pages scaled down to a fraction of their size, so the changed link is a few pixels tall; nothing marking the change; Before and After stacked hundreds of pixels apart; three or four control groups per surface; the page starting hundreds of pixels below its heading; file names in the text.

- **Plain words only.** No file names, class names, token names, functions or paths in the file; those go in `product-design.md`. Write what a user would see: "a Changelog link in the top menu", not "`NAV` in layout.ts".
- **The top.** The ticket key and a plain title; the promise in one sentence; one sentence saying what changes; then the tabs: "How it is used" first, then the surfaces, numbered in the order of the user's path and tagged "New" or "Changed". One tab is on screen at a time.
- **How it is used, first.** The first tab is the use at a glance: who it is for; the main scenario as numbered steps of a few words each, every step opening the surface where it happens; the count before and after; what this version leaves out. Nothing else is on it.
- **Each surface, in this order:** one sentence saying what changes for the user; the changes as a numbered list of a few words each; one row of controls; the page; the other option. Nothing else above the page.
- **Actual size, never shrunk.** Render each page at 100 percent: at the width it is designed for, or at the width available when that is narrower and the page keeps the same layout. Narrower than that, the frame scrolls sideways; it never scales down.
- **Before and After flip in place.** One switch on one frame, After selected first. Never side by side and never stacked.
- **Mark every change.** In After, each changed element gets a strong outline and its number from the list, in one color the product does not use. A surface marked "New" has no outline, since all of it is new.
- **At most two controls in the row:** Before and After (or the state, for a new surface), and the device (desktop, phone; desktop app and web app when they differ). Show only the states this ticket changes; a state whose Before and After differ exactly as in another state is left out.
- **Alternatives under the page.** "Other option considered: its name. Why it was not picked, in one sentence." and a "Show it" button. While it shows, the page says so and offers the way back to the recommended design.
- **On a phone,** start each surface in its phone layout.
- Surfaces with no current version show only the new design, marked "New".

The frame and the marks, in outline:

```js
// The page renders at 100 percent; a narrow window scrolls sideways instead of shrinking it.
const fit = () => {
  iframe.style.width = Math.max(minWidthForThisLayout, Math.min(designWidth, box.clientWidth)) + 'px';
};
// Each page is its own document with its own script. The outer file never reaches into it
// (in the app's panel it cannot); the page reports its height and each choice made in it.
iframe.srcdoc = `<!doctype html>...<body>${page}<script>
  const tell = (m) => parent.postMessage({ page: ${JSON.stringify(key)}, ...m }, '*');
  new ResizeObserver(() => tell({ height: document.documentElement.scrollHeight })).observe(document.body);
  // A control that changes the page: el.addEventListener('change', () => tell({ act: 'model', value: el.value }));
<\/script></body>`;
addEventListener('message', (e) => {
  if (e.source !== iframe.contentWindow) return;
  if (e.data.height) iframe.style.height = e.data.height + 'px'; // otherwise: change the state and set srcdoc again
});
// Inside the page: <a class="chg" data-n="1">, styled with
// .chg { position: relative; outline: 2px solid #e11d48; outline-offset: 5px }
// .chg::after { content: attr(data-n); position: absolute; top: -17px; right: -17px; /* a filled circle */ }
```

**Alternatives**

- When a design decision has real alternatives, build the one you recommend as the default and put the others under the page as above, each saying what differs and why it was not chosen. The person reviews the file before moving the ticket on, so a placement, a layout or a wording is flipped there, not asked.
- For a change that follows an existing pattern, build one version and no alternative.
- Two or three alternatives per decision at most, each one a real candidate. An option nobody would pick comes out.
- When building uncovers options that differ in what a user can do, add them to the batch as "Walk the user's path" says, with a screenshot per option.

**Found on the way**

- Something the ticket did not ask for that is under about two hours of building is built into the file and labeled "Also fixed here", without asking. Larger, it is one line for a new ticket in `product-design.md`, unless it belongs to what this ticket delivers; then it goes in the batch with what it costs.

**Layout practice while building**

- Secondary actions are quieter than the primary one. A destructive action takes the primary style only when destruction is the purpose of the dialog. When everything is emphasized, nothing is.
- Line height about 1.4 to 1.6 for body text and tighter for headings. Use weight and color for emphasis before adding another size.
- Never meaning by color alone: pair it with a word or an icon.
- One level of containment: no card inside a card inside a panel.
- Every interactive element has a default, hover, focus, pressed and disabled appearance.
- In a column of numbers, right-align and use figures of equal width. Put the identifying column first, order by what the user looks for (newest first when that is time), and show the count.
- Match density to the task: a table an operator scans all day is denser than a form a customer fills once.
- Icons come from the product's set at its sizes. An icon alone needs an accessible name and, unless it is universal, a visible label or a tooltip.
- Every graphic in the file is the one that will ship: the product's own icons and marks, a new one drawn as it will be built, each at the sizes a user meets it.
- Motion is short (about 150 to 300 milliseconds) and only shows where something came from or went. Honor the reduced-motion setting.
- Reserve the space for what is still loading, so that the layout does not shift under the pointer.
- Decide what stacks, collapses and moves at narrow widths. The primary action is never hidden.
- When two options both follow the rules, build the one with fewer elements.

**Token layers**

| Layer | Holds | Example | Used by |
|---|---|---|---|
| Primitive | Raw values on a scale | `gray-700`, `space-4`, `radius-2` | Semantic tokens only |
| Semantic | A role | `text-secondary`, `surface-raised`, `border-danger` | Components and layouts |
| Component | A decision for one component | `button-primary-background` | That component |

- Name semantic tokens in the design, never primitives and never raw values. Dark mode and themes then come from remapping semantic tokens, not from restyling components.
- Name by role, not by look: `text-danger`, not `red-text`.
- A scale has few steps: spacing in steps of 4 or 8 pixels, five to seven type sizes, two or three radii and elevations. A value between two steps means the layout is wrong, not that the scale needs a step.

**Template: the visual specification of a surface** (it goes under Surfaces in `product-design.md`)

```
Layout:    page heading; drop area; file list; summary and actions. Single column at every width
Heading:   "Upload files"; heading-large
Primary:   "Upload"; Button, primary
Secondary: "Choose files"; Button, quiet. "Retry failed"; Button, quiet, shown only when a row failed
Spacing:   space-6 between regions; space-2 between rows; space-3 inside a row
Color:     text-primary, text-secondary, text-danger, surface-raised, border-subtle; nothing raw
Narrow:    row actions move under the file name; the summary sticks to the bottom edge
Names:     "Choose files", "Remove results-3.csv" on each row, "Upload"
Order:     Choose files, each row's Remove, Retry failed, Upload
Status:    "3 of 5 files uploaded. 2 need attention." announced once when the batch ends
Contrast:  text-danger on surface-raised 5.9 to 1 (light), 6.4 to 1 (dark)
Targets:   Remove is 24 by 24 pixels at desktop width, 40 by 40 at narrow widths
```

**Template: the order of the file**

```
Top:   PRJ-41: upload several files at once
       Pick all your result files once and see which ones arrived.
       Two screens change. Pages are shown at their real size; each change is outlined and numbered.
       [How it is used] [1 Upload dialog  Changed] [2 Upload history  New]    <- tabs, one at a time
First: For a project member at the end of a test run.
       1 Drop the files  2 Press Upload once  3 Retry the ones that failed    <- each step opens its surface
       Five files: 11 actions before, 3 after. Not in this version: resuming an interrupted upload.
Tab 1: Choose several files at once and see a state for each. Five files take 3 actions instead of 11.
       1 Several files in one pick  2 A state on each row      <- numbered like the outlines
       [Before | After]  [Desktop | Phone]                      <- the only row of controls
       [ the page, with outlines 1 and 2 ]
       Other option considered: failed files in a separate panel. Two places to look,
       and the panel is empty most of the time.  [Show it]
Then:  Output without a screen; Also fixed here; New in the design system, each shown once
```

**Template: the words.** On the first tab: who it is for, and each step in a few words. On each surface: what changes for the user, with the count when it went down. On each numbered change: the element and what happened to it, in four to six words. On each alternative: its name, then why it was not picked, in one sentence.

**Template: output without a screen, before and after**

```
Before
$ tool upload *.csv
$ echo $?
0

After
$ tool upload *.csv
uploaded  results-1.csv
uploaded  results-2.csv
failed    results-3.csv  6.2 GiB is over the 5 GiB limit per file. Split it and upload the parts.
2 of 3 files uploaded. 1 failed.
$ echo $?
1
```

**Questions to ask here**

- How does someone actually use this? Show the steps.
- Is the before the real thing, or what you remember?
- What changed here? One line.
- Are these the product's styles, or new ones? Don't hard-code a color.
- Do we already have a component for this?
- Is this a new token? Is it needed? Skip it until we know we need it.
- Can a reader click it? Can they see the error state?
- Why are there two files? One file.
- What is the point of this alternative? If nobody would pick it, remove it.
- How does it look on a phone?
- Where is the change itself? Can a reader see it?
- The preview is too small. Show it at its real size.
- Too crowded. What does a person need to look at?
