Applies when the repository uses Spark with Delta tables, Databricks notebooks or asset bundles.

- Test against a local Spark session with small real Delta tables in a temporary folder. Do not mock DataFrames.
- When a notebook cannot be imported, extract its functions from the source with `ast`, execute them into a namespace, and test them. Notebook logic is not exempt.
- Build input from `minimal_valid_payload`; each test then removes or breaks one thing.
- A row with a null in a non-nullable field lands in the exceptions table with its raw payload and the list of null fields. A conformed row keeps its source payload.
- Typed nulls: read the table's schema after a batch in which every payload lacks an optional path. A bare null materializes the column as VOID in the Delta table when every payload in a batch lacks the path, and only a regression test pins it.
- A known limitation gets a test that shows the safe side: for example, that rejected rows keep their exact source bytes, so nothing is lost.
- A validity rule is a named function that returns a `Column`: test it with a row for every producer version in its docstring, including a row with no version.
- A target that is not ready fails the bundle validation on purpose; do not make it pass.
- Live, on the development target: record batch counts and elapsed seconds, and read the merge plan for the partition columns in the condition.
- A one-off backfill notebook is deleted once run, and its log is evidence, not a file to commit. A production run belongs to the person: write the command, the lookback and the comparison query, with the counts that prove it.
