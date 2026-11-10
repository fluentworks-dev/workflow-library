Applies when the repository uses numbered migration files applied by a migrator.

- When the default branch took this ticket's number, renumber the file and the migrator's journal. Never merge a duplicate, and never edit a migration that merged.
- One transaction per file; a concurrent index build is a hand step.
- A new non-nullable column on a table with rows is three pull requests: add it nullable, backfill, tighten. A rename is add, write both, backfill, read the new one, stop writing the old one, drop: each its own deploy.
- Additive: migrate, then deploy. Contracting: deploy the code that stopped reading, then migrate in a later pull request.
- Before any drop or reload, tables that are not seeded are backed up. The drop is the person's step.
