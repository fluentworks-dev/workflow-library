**Ask now, once.** A point earlier stages left for the person goes in one AskUserQuestion batch before you save, only when the design cannot proceed without the answer. Apply the answers, then save.

Save code-design.md as a ticket document with these sections, in this order. Keep a section that has nothing in it, with "None".

| Section | Holds |
|---|---|
| Status | `draft` or `final`, the revision number, and what it replaces |
| Intent | One paragraph: the problem as someone meets it in the code today, then the key idea |
| What it looks like | Numbered usage examples as real code, before any abstraction |
| Concepts | One entry per concept added, changed or removed |
| How the pieces relate | The diagram with existing parts marked, then the concepts whose meaning shifts |
| Interfaces | Each interface as real code with its error behavior, and where each rule is enforced |
| Failure behavior and proof | The table of cases, each with the test that pins it |
| Decisions | Decision, Choice, Reason, Decided by. Includes the shape that lost |
| Anti-goals | What the code must not do even by accident, each a sentence a reviewer can check ("A second place to define a property.") |
| Seams | What is deliberately not built, with its contract, named so nobody builds around it |
| Facts the builder can rely on | Each with file and line and where it was verified. Assumptions marked |
| Global constraints, File map, Review focus, Tasks | The plan |
| Verification | Done means: the exact commands and the observable outcomes that prove the whole change (the tests pass, a named invalid input fails naming the rule, an import pulls in nothing it should not) |
| Coverage of scope.md | Each outcome, with the tasks and tests that deliver it |
| Out of scope (do not implement) | Enumerated |
| Left to the builder | What is intentionally unspecified |
| Decided for you | Each choice made without asking, one line |
| Open questions for the Software Design Review | Where an earlier design is wrong or cannot be built as written |

A filled head, abridged:

```
Status: draft, revision 2 (replaces revision 1, which defaulted a blank name in the backend)

## Intent
A person attaching a file cannot say what the file is for, so every attachment lands under the
upstream service's default name and the project page cannot tell a drawing from a scan. The
upstream service already accepts an association name. This change carries one through,
unchanged, and never invents one.

## What it looks like
1. A named attach: `POST /v2/projects/{id}/files {"file_id": "f1", "association_name": "drawing"}` returns 201.
2. A blank name is omitted, so the upstream default applies: `{"file_id": "f1"}` is forwarded
   with no `association_name` key at all.
3. A whitespace-only name: `{"file_id": "f1", "association_name": "  "}` returns 422,
   "association_name must not be blank".

## Concepts
- `AssociationNameField` (new, `src/models/requests/validated_fields.py`): the one definition
  of a valid association name. Knows the length limits and rejects whitespace-only values.
  Used by every request model that carries the name. Depends on nothing.
- `AttachFileRequestV2` (changed, `src/models/requests/v2/files.py`): gains an optional
  `association_name`. Before: the file identifier only.

## Decisions
| Decision | Choice | Reason | Decided by |
|---|---|---|---|
| A blank name | Omit the key; never default it | The upstream service owns the default; a second default could disagree with it | The person |
| Where the rule lives | An annotated type at the API boundary | Holds for every client, not only the upload form | The agent |
| The shape that lost | A validator on `AttachFileRequestV2` | Would be copied into the next model that carries a name | The agent |

## Anti-goals
- Do not default a blank name to anything.
- No list of allowed names in the backend. The upstream service owns the vocabulary.

## Seams
- Renaming an existing attachment: not built. Contract when it is: `PATCH` on the attachment
  with the same field type.

## Left to the builder
- The exact wording of the 422 message, as long as it names the rule and the value.
```

Before saving:

1. Reread it against scope.md, product-design.md and system-design.md, those that exist, and fix every contradiction. One concept has one name across all the documents.
2. Reread it for any sentence that can be read two ways. Pick one reading and write it.
3. Check every path, name and signature in it against the repository once more, and search it for placeholders.
4. Confirm that every outcome in scope.md appears under Coverage.

When you revise a saved code-design.md, edit the copy that reading it saved and save it from that path, with a status line saying what it replaces; write it from scratch only when its concepts change. Do not append notes.

Do not ask for approval. Your summary gives the shape of the change in one line, names what the person decided, and links code-design.md.

### Questions to ask here

- Could someone build this without asking the person anything?
- Does the example come before the explanation?
- Which sentence here will be false after the first review round?
- Is each anti-goal something a reviewer can check?
- What is the builder allowed to decide? Is it written?
- Same word for the same thing in every document?
