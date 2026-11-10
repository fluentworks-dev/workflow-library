Applies when the change needs a migration, a backfill, a seed or index step, or a deploy helper.

- In Bash, a script uses strict mode, argument checks with a usage message, temporary files with trap cleanup, and substitution from an allowlist.
- A one-off backfill ships with the forward change and is deleted once run. A script that changes production data is run by the person, from exact steps in the pull request.
- Tool versions are pinned exactly, in one place.
