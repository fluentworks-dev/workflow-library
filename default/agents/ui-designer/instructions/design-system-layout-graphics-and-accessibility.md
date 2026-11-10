**Design system**

One fact has one home. On a surface that home is the design system.

- Find it before designing: the repository's guidance file and design documentation, theme files, tokens, CSS variables, the Tailwind or equivalent configuration, the shared component library, and two or three existing surfaces like the one you are changing.
- Reuse what exists. Take color, type size, spacing, radius, shadow and icons from tokens; never hard-code a value. Use an existing component before a new one.
- When tokens or assets are generated from a source file, change the source and regenerate. Never edit generated output by hand.
- When something needed is missing, add it to the shared system with a name that fits, and say what is new and why an existing piece would not do. When there is no design system, define a small one first (semantic colors, a type scale, a spacing scale) and use only that.
- Consistency with the product beats a locally better idea: existing project patterns come before personal preferences. The same thing looks, reads and behaves the same everywhere.
- When the existing pattern is wrong, fix the pattern, in scope. Two styles for the same error is a defect.
- Port a sibling surface's pattern and say which one.
- No variant just in case: skip a feature until it is known to be needed. Code repeated by hand for one component is extracted into a shared component at its third copy.
- Things that look alike but mean different things stay separate: a warning color and a brand accent that share a value today are two tokens, with the reason written at the token.
- A rename is not free: settle names in the design document before anything is built on them.
- Practice: a design names semantic tokens (a role), never primitives or raw values.

**Layout and visual**

Pleasing means ordered: the eye finds the heading, then the main thing, then the action. Then stop adding.

- One spacing scale, in steps of 4 or 8 pixels. Related items sit closer together than unrelated ones. Padding is the same across similar containers.
- Align to a grid or shared edges; no near-misses of a few pixels.
- One clear heading and one primary action per surface. Build hierarchy with size, weight and spacing before color.
- At most two font families and a few sizes from the type scale; body text 14 to 16 pixels; lines of roughly 60 to 80 characters.
- Mostly neutrals, one accent for primary actions and focus, semantic colors only for meaning. Dark mode if the product has it.
- Whitespace before borders and dividers. No decoration that carries no information.
- The same thing looks and behaves the same everywhere: buttons, inputs, empty states, loading states, errors.
- Layouts work from a narrow phone to a wide desktop; nothing overflows or overlaps.
- A page never appears unstyled or half arranged and then jumps.

**Graphics**

An icon, an illustration, a diagram, a chart, an image and a brand mark are part of the design and are held to the rules a word is held to.

- One home: a graphic comes from the product's set or is generated from its source file. Change the source and regenerate; never edit the output, and never redraw a mark by hand.
- The smallest thing that works: a graphic stays only when it tells the user something faster than words can.
- One meaning: as one concept has one name, one state has one icon across the product.
- Never alone: a word, a label or an accessible name goes with it, and it is never the only sign of a state.
- Proved by looking: rendered at the smallest size a user meets it, in light and in dark when the product has both. A change nobody can see at its real size has not been shown.

**Accessibility**

Meet WCAG 2.2 level AA in the first draft, not when a reviewer asks.

- When adding a control, give it an accessible name that is the label the user reads. Tests select it by role and label, the way a user finds it.
- Text contrast at least 4.5 to 1, and 3 to 1 for large text and for controls. Visible focus that nothing covers. Full keyboard operation. Semantic elements and labels. Targets at least 24 by 24 pixels, and 40 for primary and touch targets. Never meaning by color alone. No sideways scrolling at 320 pixels wide. Status messages announced.
- Output without a screen has the same duty: no meaning by terminal color alone, an email that reads as plain text.
- An existing pattern that fails these is wrong: fix it where the ticket touches it and report the rest.
