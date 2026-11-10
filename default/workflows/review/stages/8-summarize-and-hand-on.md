Post one summary comment on the pull request. That comment is the record of this review; nothing is saved as a ticket document.

Template:

```
## Review summary (round <n>)

**Verdict:** <one line: ready for validation, or what blocks it and with whom it sits>

### Found and fixed
- [blocker] `file:line` <finding>. Fixed in <hash>. Covered by <test>.
- [simplification] `file` <finding>. Fixed in <hash>. Removes <what>.

### Stands, and why                 (or "None")
- [severity] `file:line` <finding>. <declined on a verified fact | asked of the person | waits for ...>

### Follow-ups                      (or "None")
- [follow-up] <what, where, enough detail to file>

### Verified, no change needed
- `file`: <what was verified and how>

### Threads                         (or "None open")
- <n> answered: <n> fixed, <n> declined, <n> deferred

### Files
- Clean: <list>
- Not reviewed: <generated, lock and binary files, by name>

### Decided for you                 (or "None")
- <a choice made without asking, one line each>

### What I ran
- `<the test of each fix>`: <result>; it failed before the fix
- [ ] <what could not be run here, and why>
```

Filled example:

```
## Review summary (round 1)

**Verdict:** Ready for validation. Two blockers found and fixed; one follow-up belongs to the data lake.

### Found and fixed
- [blocker] `loaders/recent_runs.py:88` truncated to the limit before sorting, so a job could show an arbitrary status. Fixed in 1a2b3c4. Covered by `test_recent_runs_returns_the_newest_status_when_rows_arrive_unsorted`, confirmed failing on the parent commit.
- [blocker] `loaders/recent_runs.py:61` interpolated the `status` filter into SQL after quoting only. Fixed in 1a2b3c4: bound parameter, field name checked against the loader allowlist. Covered by `test_status_filter_with_a_quote_matches_nothing`.
- [simplification] `loaders/recent_runs_cache.py` wrapped the library cache for one caller. Fixed in 5d6e7f8. Removes the file, `RecentRunsCache` and its two tests.
- [nit] `get_data` renamed `load_recent_runs`. Fixed in 5d6e7f8.

### Stands, and why
- None.

### Follow-ups
- [follow-up] The query still scans seven days of partitions per request. This change only stops the bleeding; the durable fix is repartitioning in the data lake's repository.

### Verified, no change needed
- `schema/job.gql`: the new field is additive, and the existing dashboard queries still validate against the schema.
- `loaders/recent_runs.py`: two concurrent lookups produce exactly one query, with the cache and the batching running for real.

### Threads
- 6 answered: 3 fixed, 2 declined on verified facts, 1 no longer applicable.

### Files
- Clean: `schema/job.gql`, `resolvers/job.py`, `tests/unit/test_job_resolver.py`
- Not reviewed: `poetry.lock`

### Decided for you
- Kept the seven-day partition window: a job that has not reported recently still resolves through the full-history fallback.

### What I ran
- `poetry run pytest tests/unit/test_recent_runs.py -k "unsorted or quote"`: 2 passed; both failed before the fixes
- [ ] The query against the production warehouse, for timing: no access from this session
```

Then:

- Verify the description once more against the final branch.
- Leave the pull request's draft or ready state as it is.
- When a blocker stands, the verdict line says what blocks and with whom it sits.
- When there was nothing to review, the comment is three lines:

```
## Review summary (round 1)
Nothing to review: this pull request only swaps the upstream SDK pin from the development build `0.0.1.dev97` to the release `0.5.3`.
Verified: the lock file changes match that one pin, and the release `0.5.3` contains the upstream change it was pinned for.
```

After approval and before merge, a sweep of every surface the change touches may still find something: that is a new commit, announced on the pull request, never merged quietly.

Your summary for the ticket gives the verdict in one line and links the comment.

### Questions to ask here

- Is there a trade-off to know?
- What is still open, and with whom?
- Is every number here something I ran?
- Would a reader who never saw the diff know what changed?
