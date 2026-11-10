For any operation that changes state and can be retried, restarted or interrupted, answer by running it: what happens if it runs twice, and what happens if the last run stopped at any point.

When a write can be replayed, the write itself must be idempotent, and you test the replay instead of assuming it:

- A transaction identifier scoped to the checkpoint lands a replayed batch at most once: `test_replayed_batch_id_writes_nothing_twice`.
- A fallback for a failed batch, then a replay: `test_fallback_then_replay_keeps_rows_out_of_job_state`, with the invariant in the docstring.
- An update-only merge for a backfill (idempotent, never inserts or changes membership): run it twice; count and membership do not move.
- Interval windows `[start, end)` with a settlement lag: a row at each edge lands in exactly one window, and data inside the lag is not processed twice.
- Uploads staged and then attached, deduplicated by checksum: the same file twice gives one stored object.

When two writes can fail between them, the order must be the one that heals itself on the next run, and the order is written down at the writes:

- "Write job state before the summary so a failure between the two writes leaves the summary without `is_complete=True`, making the next run self-healing"
- "Job state first, then exceptions: that order duplicates rows rather than losing them"

When no sentence states the order, prove which order loses data, and have the order and its reason written at the write. Duplicated rows can be recovered; lost rows cannot.

Concurrency to exercise:

- Two writers read one version and both write: the second gets the conflict the contract promises, and nothing is overwritten silently.
- A parent and child created together: fail the child, and no parent remains. A link that can be set once: the second set is refused. A lock with a time to live: a dead holder's lock expires.
- Parallel reads that fan out: one fails, all settle, and the first failure surfaces.
- A cache per request: parallel reads cause one fetch. Without it, an expired token is redeemed once per read, in parallel, and the identity provider throttles the refresh.
- A call that can outlive its caller's timeout returns at once, its background work must never raise, and the caller polls observable state. Kill the work and poll.
- A queue fed by a callback: when full it drops, logs the drop at most once per interval, and the consumer blocks with a short timeout instead of sleeping, because a sleep throttles the consumer to one batch per interval.
- A batch that partly fails lists every failed item with its error, and success is false.
