**Forms**

- Validate when the user submits, not while they type. Once a field shows an error, clear it as soon as the value is valid.
- When the server returns an error to a form, keep it visible until the next real submit. Smaller actions on the page (add a row, pick a suggestion) must not wipe it: the user is still fixing it.
- An unchanged control must not mark the form as changed: choosing the value a dropdown already had leaves the form clean.
- A suggestion fills the blank row instead of adding a row.
- When a blank optional field is submitted, leave its key out of what is sent: never null, never an empty value, so the default of the system that owns it applies. Do not default a blank name to anything. Emptying a field that holds a stored value keeps the stored one; say so beside the field.
- When a rule is owned by the service, do not copy it into the interface: remove the constraint from the form and rely on the service to enforce it. Suggestions are fine; requirements are not. The copy in the interface is the one that silently blocks a value the service accepts.
- A list of suggestions says that it is one: the source of truth is the upstream service's list, and the one in the form is a set of suggestions, not a constraint. The field still accepts a typed value: let the user choose or type.
- A rule that must hold for every client lives at the service boundary, so the rule holds for every client rather than only the form. The form validates only what it needs to send a well-formed request.
- When a user can change something, give that change its own action and its own surface (archive, edit, tag), with its own submit and its own place for an error. The detail page only reads.
- Test a limit before the work: refuse an oversized file when it is chosen, not after minutes of uploading.
- Practice: one column; visible labels above fields, never a placeholder as the label; ask only what the task needs; accept input liberally and normalize it; errors beside their fields in words; a primary button that names the action.

**Wording**

- One concept has one name on every surface: never "sign in" here and "log in" there.
- Use the user's word. When the stored name differs, record the mapping.
- A label that needs a slash names two things.
- A name says where a value comes from when that matters: the email shown for the signed-in user is the one the server resolved (`viewerEmail`), so two spellings are never compared.
- Full words, no internal codes. An opaque identifier appears only beside its name, so a reader does not have to look it up.
- Speak in the domain's plain language: what happened to the user's thing, not which component failed.
- Numbers are exact and carry units: "3 of 5 files", never "some".
- A name that enters a shared space is qualified: a name that will be global to all data in the organization needs a more specific name.
- An action is named by its verb: function names are verbs or verb phrases because functions perform actions. So are buttons and commands.
- Say what is deliberately absent: for example, order tags are deliberately not editable for now.
- No placeholder text and no typo reaches a hand-off. A typo in a label becomes a typo in a test, a fixture and sometimes a stored name that can never be corrected.
- Practice: plain-language content design. Sentence case, a verb and its object on a button, a link that says where it goes, no blame, dates that cannot be misread.
