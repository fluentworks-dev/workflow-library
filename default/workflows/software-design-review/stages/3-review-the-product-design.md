Skip this stage when product-design.md is a true three-line document. When the design is text only (no file), review the wording in the document against the surfaces it names and go on. Otherwise open the interactive design file, go through each scenario from start to finish as the person, without the document beside you, then check each lens and note every finding with the surface it is on.

- **The use.** The promise is one sentence the person would say. Each scenario can be done from its examples alone. Every concept the person must hold is needed: one that can be derived, merged or left for later is a finding. The change lives where the user would look for it: an existing place when one is that place, a new one when none is, and the document says which and why. The first version is narrow and says what it leaves out.
- **Least work.** Count the steps, decisions and typing on the path. Each step that can be removed, merged, inferred or given a safe default is a finding. One question at a time.
- **Every state.** Empty, loading, partial, error, too many, too long, disabled, no permission: each has its wording. A label that depends on time or size has its number ("running" means a status within 5 minutes).
- **Errors the person can act on.** Wording is chosen for the next action and lives in one place. A missing item and a denied item read the same. The service's own sentence is shown for a problem the person can fix; no internal detail is shown for a fault of the system. A heading names the class of failure.
- **Forms.** Validation on submit, not while typing. A server error stays until the next real submit. Nothing typed is lost. An untouched control does not mark the form as changed. A blank optional field is sent as nothing, so the service's default applies. A field that cannot change is shown locked with the reason, not hidden.
- **Unknown values** are shown as they are.
- **Rules owned by the service** are not copied into the interface as requirements. Suggestions are fine.
- **Write paths.** When the product has not decided who may change something, the design shows it, holds the change and says so.
- **The step that cannot be undone** is separate, and the person is told in plain words what it will do before it happens. A cost is confirmed before anything is created.
- **Design system.** Color, type, spacing, radius, shadow and icons come from tokens. Components are reused. Anything new is named, added to the shared system and listed. One primary action per surface.
- **Graphics.** Each one tells the person something, comes from the product's set or its source file, has a text alternative, and can be read at the smallest size it is shown.
- **Accessibility.** WCAG 2.2 AA: contrast, visible focus, full keyboard operation, an accessible name on every control, target size, no meaning by color alone.
- **Output without a screen.** Command-line text, operator messages and emails have one format, name the class of failure and the fix or the person to contact, and show no stack trace to someone who cannot act on it.
- **Waiting.** What the person waits for, how long, and what they see meanwhile. A list that grows is paged.
- **Buildable without guessing.** Every state and its exact wording is written down, and every alternative behind a switch is labeled with the default.

Check the whole against Nielsen's ten usability heuristics, and name the heuristic a finding rests on. Judge the path, the states, the wording and the consistency; visual detail inside a component belongs to the design system and the designer, so where you did not judge it, say so under "Not checked".

**Questions to ask here**

- How many things does the person need to know to use this? Can one go?
- Can the person get this done in one step fewer?
- What does the person see when this fails, and can they act on it?
- Can this constraint go from the interface, with the service enforcing it?
- Has product decided the write path? If not, display it only, without creating, for now.
- What happens to what they typed?
- Does a blank mean nothing was given, or an empty value?
- What does it show for a value it has never seen?
- Is this a new component, or do we already have one?
