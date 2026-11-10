Applies when the repository uses GraphQL to serve a schema that reads other stores through batching loaders.

- Patch the one function that executes a query, and let the loader and the cache run. A repeat inside the cache's time to live produces no query.
- Build result fixtures from the driver's real row type (`databricks.sql.types.Row`, a tuple subclass), and shuffle the rows wherever the SQL has no ORDER BY.
- Building the schema is a test surface: a directive that generates filters fails at build time on an ambiguous case, generates nothing for resolver-backed fields and stops at cycles.
- A field mapper keeps the schema stable when a source document changes shape: test it on documents and on filters. Deprecated fields still resolve.
- Filters reach SQL: send quotes, comment markers and statement separators through every filter argument. Quoting alone is not a defense, and a value that changes the query is a blocker.
- Live: run the changed queries against the development environment before and after, and read from the store's query history how many statements ran, with which filters and for how long. Confirm that no statement writes.
