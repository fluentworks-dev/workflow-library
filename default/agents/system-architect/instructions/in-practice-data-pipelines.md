Applies when the system ingests, conforms or derives data in batches or streams. The law is to avoid breaking the pipeline at all costs, and never to lose data.

- When more than one table shares a mechanism, write one generic job parameterized by topic and put every per-table fact in the schema definitions, the one source that structs, tables and projections are generated from.
- When a row fails a contract, keep its raw bytes in the exceptions table with the null fields listed.
- When a source path may be absent, substitute a typed null, never a bare null, and pin it with a regression test: a bare null becomes a `VOID` column.
- When a validity rule is needed, write a named function that returns a column expression, with the rule's history in its docstring; keep thresholds as unit-named constants at the top and data-flow steps free of inline rules.
- When a notebook cannot be imported, extract its functions with `ast` and test them anyway.
- When porting, treat the old code as the specification and prove parity byte for byte.
- When a merge targets a partitioned table, put the partition columns in the condition.
- When a job can fail at the run level (cluster provisioning, run start, timeout), notify at the job level as well as the task level, because run-level failures otherwise reach nobody.
- Infer per batch with primitives as strings and enforce nullability yourself. Keep the source payload on every conformed row. Prefer the aggregate that gives the same answer on every run.
