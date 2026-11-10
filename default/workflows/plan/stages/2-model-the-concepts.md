The code should read as a model of the thing it is about: a person who knows the product can guess what exists, what it is called and where it lives. The aim is the most done with the least code, with every abstraction earning its place in one clear picture.

**List the concepts** this change adds, changes or removes, in the words people already use. Take the words from scope.md, product-design.md and the repository's terminology section. Where those disagree, pick one word and note the rename.

- Use the word for the real thing (an order, a run, an upload, a tag), not one invented for the code (a processor, a helper, a manager of handlers).
- One concept has one name everywhere: `patches` never `operations`, `document` never `node`, `entity_type` never `level`. A rename is one commit across the repository, with the old and new names in its body.
- A name you cannot write without "and", "data", "info" or a number suffix means the concept is wrong or split. Split or merge it before going on.
- A name that enters a namespace other teams share (a table, a topic, a package, a metric) is qualified, because the name is global to all data in the company.
- A name says where a value comes from when that matters: `viewerEmail` for the address the server resolved, `uploader_email` resolved by one precedence rule in one place.
- New terms go into the repository's terminology section ("Use these words and no synonyms"), in the task that introduces them.

**Describe each concept** as a CRC card (class, responsibility, collaborators):

- its name and file, and whether it is new, changed or removed;
- its one responsibility, in a sentence without "and";
- what it knows and what it does;
- who creates it and who calls it, and what it depends on;
- for a changed concept, what it was before.

Show the core shape in a few lines of real code (a signature, a type, a table) only where words would be vaguer. Skip private helpers, tests and edits that leave a concept as it was.

**Test the model** in this order, and change it until every answer is yes:

1. Does each concept match a thing in the product's real world, under the name people already use?
2. Could a newcomer guess where each thing lives and what it is called?
3. Does each fact have one home, with the rest computed from it? A stored `id` that is derivable from the slot key can be dropped.
4. Is every invalid state impossible to write down? A discriminated union, not a flat shape where nothing stops a card payment from carrying `bank_account_number`; a closed set of literal keys, not a free-form map.
5. Does one concept have one name, and do two different concepts have two types, even where their fields match today?
6. Is the data as flat as it can be? An extra `address` namespace around two fields is probably not useful: flatter JSON, and one less model. A list of named entries can be a map whose key is the name.
7. Is every existing concept whose meaning shifts listed (a name that now covers more, a flow that gains a step, an invariant that changes)? These are what break a reader's assumptions.
8. Does the next change of the same kind have an obvious home? Name that next change (a fourth variant, a second backend, one more field) and point at where it will go. Name what you deliberately do not build as a seam with its contract, so nobody builds around it.

**Keep different concepts apart.** Shared code implies shared meaning. These stay separate even when they look identical today, each with its reason written where someone would merge them:

- one response class per handler: the method that publishes an update does not know whether the job succeeded; the `run` method is what decides whether a job exited successfully;
- a logical schema and a physical schema; an API request model and a domain model; a stored document and a response;
- two settings that read the same string and mean different things (a storage path that already exists, and a tag value that may be edited).

Two copies that must stay identical and cannot share code (two notebooks that cannot import each other, two bundles) both carry a "keep the two copies in step" comment, or "Must match <path>", and extraction waits until a third consumer exists. The defense when challenged is the fact: "both copies carry a 'keep the two copies in step' comment, and neither notebook can import the other". Where another service depends on a file name or a string, plan a guard comment at that line.

**Prefer the data model to a heuristic.** Between a correct data model that needs a migration and a heuristic (matching text, inferring a state from other fields), choose the data model. When a shape changes a data interface, name every layer it touches: the stored schema, the access layer, the service interface, the generated clients, the surfaces.

**Then remove.** Try deleting each new concept in turn; if the change still works without it, it goes. Count the concepts in your first draft and in your final one. The final count is lower or equal, or each addition has a row in Decisions. A good revision removes a concept.

**Draw how the pieces relate**: a small text or mermaid diagram of the new and changed parts among the existing parts they touch, with existing parts marked "(existing)" and arrows for who creates or calls whom.

A worked example, for a change that lets a person name what an attached file is for:

```
AssociationNameField (new, src/models/requests/validated_fields.py)
  Responsibility: the one definition of a valid association name.
  Knows: the length limits. Does: rejects whitespace-only values.
  Created by: nobody; it is a type. Used by: every request model that carries the name.
  Depends on: nothing.
AttachFileRequestV2 (changed, src/models/requests/v2/files.py)
  Responsibility: the body of an attach request.
  Before: the file identifier only. Now: also an optional association name.

upload form (existing) --posts--> attach_file_v2 (existing route, changed)
                                    | parses the body as
                                    v
                         AttachFileRequestV2 (changed) --uses--> AssociationNameField (new)
                                    | forwards; a blank name is omitted
                                    v
                         upstream attach call (existing; owns the default name)

Meaning that shifts: "attach" meant "link a file"; it now means "link a file under a name".
Model tests: the default has one home (upstream). A blank name cannot be stored, because the
key is omitted. No `AssociationName` class: a constrained string is the whole concept.
Removed in this draft: a backend table of allowed names (the upstream service owns them).
```

When the change adds or changes no abstraction, say so in one line.

Decide names yourself and list each under "Decided for you". Ask the person only when the choice is a word a customer will read and two candidates carry different product meanings.

Industry practice behind this stage: ubiquitous language (Domain-Driven Design), information hiding, "make illegal states unrepresentable", Kent Beck's rule of fewest elements.

### Questions to ask here

- Can the `id` be dropped? Is anything stored that the structure already gives us?
- Can this list be a map, keyed by the name?
- Is this extra namespace useful? What does this wrapper add?
- Nothing stops this type from carrying the other type's fields. Should be a union on the type field?
- Can this be non-nullable?
- Two names for one thing? Pick one; a loaded term such as `operations` loses.
- Does this layer need to know about that? It only cares about files.
- Is this name global? Then it needs a qualifier.
- What does this mode add on top of what is already there? Could it be dropped?
- Fewer concepts than the last draft, or more?
