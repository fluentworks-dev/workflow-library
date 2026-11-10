- **Keep the stored shape, the request shape and the response shape as separate types**; they change for different reasons.
- **When a rule must hold for every consumer**, enforce it once at the API boundary as a reusable constrained type: the schema constraint plus a validator for what the schema cannot express, so the rule holds for every client rather than only the form. The interface never duplicates a rule the service owns; it may suggest, never require.
- **When a fact can be derived from the structure**, compute it; never store it.
- **When a field moves or is promoted**, add a read-time fallback, so existing data keeps working without a backfill job.
- **When data is temporal and old versions are served**, payload models allow unknown fields (under `forbid`, any stored document version carrying an old field name becomes unreadable); envelopes forbid them; patch models ignore them.
- **When a timestamp is compared as a string**, always serialize `+00:00`: only `+00:00` sorts chronologically against fractional seconds.
- **When a value object is not a wire model**, make it a frozen plain type. Validation libraries stay at the boundaries; a core library has no runtime dependencies.
- **When two names could mean one thing**, pick one, rename everywhere in one commit and list old and new in its body (`patches`, never `operations`, a loaded term). Put a value's origin in its name (`viewerEmail`, `requester_access_policy`).
- **When designing an authoring API**, make calls keyword-only, pass definitions as objects and external names as strings, and make a bare type in a body an import-time error with a message naming the schema and field.
- **When classifying a change**, be conservative and consumer-neutral: any nullability change and any new non-nullable property is breaking.
- **When a blank optional value is submitted**, omit the key so the owner's default applies. Do not default a blank name to anything.
- **When the choice is a correct data model with a migration or a heuristic** (matching text, inferring a state), choose the data model.
- **When definitions are the source of truth for several systems**, keep environment facts out, in the deployment target and the job: a system is a logical node, not a deployment.

## In practice: Python services

Applies when the repository uses Pydantic models for an API.

- Three model families per entity: stored documents, requests per version, responses per version.
- Reusable field aliases (`NameField`, `DescriptionField`) carry the constraint in the schema plus a validator for what the schema cannot express.
- `extra` is chosen per model; a read-time fallback is a `model_validator(mode="after")`; `exclude_none` keeps a blank value off the wire; a value object such as `Principal` is a frozen dataclass.
