## Compatibility

A design that changes a contract lists that contract's consumers and what each sees before, during and after (expand and contract).

- **Add first**: a new nullable column; a `_v2` table under a stable logical name in place of a layout rewrite; a column deprecated by comment and alias, not dropped; a reader gated on the producer's version by a named predicate, with the version history in its docstring.
- **When old data does not match the new shape**, prefer a read-time fallback to a backfill. **When a migration is unavoidable**, write an idempotent script with a dry run and bounded concurrency, safe beside the live service.
- **When a setting is renamed**, keep the old name as an alias so an existing deployment needs no configuration change.
- **Before removing anything a consumer might use**, search the consumer repositories, deprecate first and sequence the consumer changes. If a caller surfaces later, restore the contract as deprecated. Justify a removal with facts: no caller passes it, and wire-level compatibility is preserved.
- **When a change is breaking**, say so in bold in the pull request and give the sequence across repositories. **When a rename would break history**, accept it only with its migration step named.
- **Contracts get deprecation; code gets deletion**: internal code that is no longer used is removed.

## Seams

- **Build a seam when a second backend is real or a vendor type leaks**, never speculatively.
- **When an SDK type would cross a boundary**, hide it behind an abstract base class, chosen over a structural protocol because mypy verifies each override's signature against the base at class definition, and Python refuses to instantiate an adapter that is missing a method.
- **The error contract lives in the base class docstring**: create of an existing identifier raises already-exists; get of a missing document returns nothing; replace or patch of a missing document raises not-found; a stale version tag raises a concurrency conflict; a failed condition without a tag raises precondition-failed.
- **The factory imports adapters lazily** so an Azure deployment never imports pymongo or aiobotocore, selects once at startup, and rejects an invalid layout with a message that explains why.
- **A parity suite runs one scenario set over every adapter**, and semantics are checked on the real engine: for example, against mongo:7 with string, null, missing, numeric, array and scalar values.
- **Queries are an expression tree and patches are typed operations**, compiled per backend. An adapter is named for the API it speaks (`mongodb`, not `documentdb`).
- **Deploy-time facts are data**: indexes are declared as data and applied by a script at deploy time, and the server never runs DDL; so are secret names and seed rows.
