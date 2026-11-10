Decide these in the design, so the builder does not guess them and a reviewer can check them. Where the repository has already chosen, follow it.

**Types**

- Type everything in library and service code, in current syntax. Notebooks are the exception: annotate at least their predicate functions.
- A closed set of values is a closed type: a literal or string union for a selector, so a typo fails at settings load; an enumeration for a vocabulary the code owns. Closing it is a decision: adding a value is visible to consumers (publish the SDK, upgrade the consumers, then emit the new value). A field another system owns is a string, and unknown values pass through.
- A constraint that must hold for every client is one named type at the boundary: `ResourceName = Annotated[str, StringConstraints(strip_whitespace=True, min_length=1, max_length=80)]`, so the rule holds for every client rather than only the form in the interface. The rule: a constraint in the schema, plus a validator for what the schema cannot express.
- Polymorphic data is a discriminated union: one variant per value, a discriminator that is required and has no default (an optional discriminator generates broken SDKs), internal bases private, and a test that every member has a variant.
- A value object that is not sent over the wire is an immutable plain type; validation libraries stay at the boundary. A core library can go further: standard-library frozen dataclasses with hand-written JSON serialization, and no runtime dependencies in the package.
- A seam with several adapters is an abstract base class, not a structural protocol, because mypy verifies each override's signature against the base at class definition, and Python refuses to instantiate an adapter that is missing a method. Its docstring carries the error contract.
- A read-only parameter takes the read-only abstract type. A helper is typed against the least it needs: `headers: { get: (name: string) => string | null }`, not the framework's `Headers`.
- A deliberate signature divergence carries `# type: ignore[override]`, with the reason written in this document. Every suppression carries its code. No `any` in a new module.
- In Python: `str | None`, `collections.abc.Sequence`, `str, Enum` with `__str__` returning the value. In TypeScript: the schema is the type source (`z.infer`), narrow string unions (`UploadTarget = "azure" | "s3"`), `readonly` fields, `as const` tuples, string enumerations with PascalCase keys.
- The type check runs inside the project's own environment, over the source tree only: a mirrored hook misses errors the project's environment catches. Pre-existing errors are counted and stated, never fixed in passing: mypy passes with only pre-existing errors remaining, and their count is given.

**Structure**

- Size follows the role: a handler is 20 to 50 lines; a method that holds business rules may be long when it reads top to bottom; a notebook is a linear script with named predicate functions at the top and numbered stage headers.
- Plan no helper that is a few lines with one caller: a private `_*` helper of a few lines used once is embedded where it is used. Plan a named function for a block that will be reread: grouping those lines into a function makes them easier to read.
- Plan no file for one constant used in one place: such a file is too small to be its own file.
- A mapping from keys to values is a lookup table, not a ladder: a constant lookup dictionary instead of an if-else chain. Name it for its key and value: `ENTITY_TAG_FIELDS`, `PARTITION_KEY_FIELD_BY_COLLECTION_TYPE`, `_REQUIRED_SETTINGS_BY_BACKEND`.
- Dispatch on a closed set of typed operations by matching on the classes (`case SetFieldPatch():`).
- Authoring and batch interfaces are keyword-only: `def write_batch(..., *, transaction_id_prefix: str)`, `export_orders(*, warehouse_config, output_file_path)`.
- Pure logic and adapters are separate modules: `storage`, `pricing` and `reports` with no command-line imports, and `commands/*` as thin adapters; `logic/` apart from `persist/`.
- No single-use indirection: a wrapper around one field, a base class with one subclass, a factory for one product.
- Code that only tests call goes in test utilities, never in production classes.

**Constants, defaults and settings**

- A constant lives at the top of the file that owns the behavior, in `UPPER_SNAKE`, with a one-line comment. A private tunable takes a leading underscore (`_RETRY_MAX_ATTEMPTS = 5`, `_RETRY_MAX_WAIT = 30  # seconds`). A value that belongs to one component is a class constant. A value an operator may change is a setting or a platform parameter.
- A default shared by a component, its launch file and a template is defined once, in the module that owns the behavior, and imported: a single source of truth. Two values that must stay equal are one constant, with a test asserting against it.
- A fixed vocabulary of strings is an enumeration, the usual good practice for managing hard-coded strings.
- Every quantity carries its unit in the name (`TOKEN_EXPIRY_SLACK_MS`, `message_retention_hours`, `kms_key_deletion_window_days`) or in a trailing comment (`_BLOB_READ_TIMEOUT = 600  # seconds (10 minutes, tuned for multi-GB uploaded files)`).
- A chosen size or window has its price or its arithmetic beside it: about USD 900 per month for the brokers; 9 = 2 x vCPU + 1 for a 4-vCPU plan. A number chosen by feel says that it is based on intuition of what is reasonable.
- A deliberate value is written out, so a reader sees it is a choice. An opaque identifier is named in a trailing comment, because a reader checking whether a value is the staging or the production registration should not have to look it up.
- A value that selects an environment (a URL, a client identifier, a tenant) is required, with no default in code; the staging value ships in the template, and a test checks that the template is self-consistent. A value that only tunes behavior is optional and defaults to the constant for backward compatibility. A value that is the same everywhere may be hard-coded and named.
- A deploy-time fact (an index, a secret name) is data applied by a script: the server never runs DDL.

**Where this departs from common practice, and why**

- Names are stricter than PEP 8 and the Google style guides, which tolerate single letters for counters: no single-character names and no abbreviations. The scope is narrower than a style guide's: new or changed lines only, the Boy Scout Rule applied narrowly. A hook that enforces it everywhere is not used, because it forces renames on lines the change does not touch.
- Duplication between different concepts is preferred to a shared type, more strongly than the usual "don't repeat yourself" teaching. Extract at three repeats still holds for one concept repeated.
- Every input is written out in every unit of configuration, against the tool's own encouragement to inherit through includes, so that reading one unit file tells the whole story.
- An abstract base class is chosen over a structural protocol for the checks it gives at class definition and at instantiation.

### Questions to ask here

- Can these two be non-nullable?
- Is this a string or a closed set? Who owns the values?
- Is this file too small to be its own file?
- Where does this default live? Is it defined twice?
- What unit is this? Megabytes or mebibytes?
- Is this value required, or does it silently point at the wrong environment?
- Why a wrapper model? One less model is better.
- How much does this size cost per month?
