Types:

- When writing library or service code, type everything in current syntax. Notebooks may annotate only their predicate functions.
- When a set of values is closed, use a literal union or an enumeration, so a typo fails at settings load. A field another system owns stays a string, and unknown values pass through.
- When a constraint is reused, make it a named type: the constraint in the schema plus a validator for what the schema cannot express.
- When data is polymorphic, use a discriminated union with a required discriminator (an optional discriminator generates broken SDKs) and a test that every member has a variant.
- When a value object is not sent over the wire, make it an immutable plain type. Validation libraries stay at the boundary ("parse, don't validate").
- When suppressing a type or lint finding, give the code and the reason. Narrowing with `assert isinstance` is fine for what an upstream layer guarantees. No `any` in new modules.
- When type errors pre-exist, do not fix them in passing or add to them; Validation counts and states them per pull request, in the form "12 errors, all pre-existing". The checker runs inside the project environment: a mirrored hook with its own dependencies misses errors.

Constants:

- When a value appears in the flow of the code, name it: `UPPER_SNAKE` at the top of the file that owns the behavior, one comment line each, a leading underscore for private tunables (`_RETRY_MAX_WAIT = 30  # seconds`).
- When a default is shared, define it once in the module that owns the behavior: a single source of truth.
- When a size or a window is chosen, put the price or the arithmetic in its comment. When a constant excludes something on purpose, its comment says what and why.
- When a value selects an environment, it is required configuration with no default in code. A tunable is optional with a documented default.
- When a deploy-time fact exists (an index, a secret name), declare it as data applied by a script: the server never runs DDL.

Structure:

- When a helper is a few lines with one caller, inline it. When a block is reread, extract and name it. When a file holds one constant used once, delete the file: it is too small to be its own file.
- When an if/else ladder maps keys to values, use a constant lookup table instead.
- When dispatching on a closed set of typed operations, match on the classes.
- When a module must be shared across bundles, keep it dependency-free and say so in its header.
- When writing a handler, keep it thin (validate, call one method, map errors) and 20 to 50 lines. Type a helper against the least it needs.

These are Fowler's Inline Function and Extract Function, and a lookup table in place of a conditional.
