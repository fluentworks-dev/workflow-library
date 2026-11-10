Applies when the repository uses Databricks Asset Bundles, Spark notebooks and Delta tables.

- CI only validates the bundle; the pipeline tests run only by hand, and the test plan carries their command and count.
- A version tag after every merge deploys production, so pushing it is the person's step; hand them the version (minor for a new table or pipeline, else patch).
- A layout change is a `_v2` table; columns are deprecated, never dropped; schema definitions deploy with, or before, the pipeline change.
- A backfill only updates (idempotent, never inserts or changes membership), is listed under Post-merge steps with its SQL, and is deleted once run. Answer the template's "Data back-fill requirements checked"; do not tick it blind.
- A rename that breaks history names its step: for example, a Delta column rename with column mapping enabled.
- Qualify a new table's name: the name is global to all data in the organization.
- Remove tracker logs, state databases, notebooks named after a person and sample data, and add the ignore pattern.
