## Failure

- **When code can detect a bad input**, fail at the earliest layer, with the rule and the value in the message: `"retry_policy must be one of {RETRY_POLICIES}, got {retry_policy!r}"`.
- **When a case is abnormal** (a file exists, a scheme is unsupported, a session is too large), raise; never tolerate, overwrite or truncate.
- **When a timeout or a wait is part of normal operation**, it is not an application error: fail the workflow only when there is a bug a developer should look at and fix.
- **When a process must survive** (a node, a user-defined function, a background thread, the 500 path of a router), catch broadly at that boundary and only there, log with context and return a failure. A cloud failure never crashes a node or blocks a run, except where swallowing it loses something silently: when a run must be tied to a job record, a missing record would silently detach the run from it, so an upload failure must fail the configure step.
- **Never lose the input.** What cannot be processed is kept as received, with the reason; a failed batch goes whole to the exceptions store.
- **Reliability beats throttling**: a throttle that does not work gives way to caching the repeated cost.
- **Every remote call has a timeout.** Retry only transient failures, and a write only when it is idempotent. Retrying every exception is a common drift; do not copy it.
- **Clean up once, in a `finally`.** A lock wraps only the mutation of state.

## Concurrency and ordering

- **When a write depends on a read**, thread the version tag into the write (`If-Match`) and map 412 to 409. **When a parent and a child are created together**, use one transactional batch. **When a multi-step write must exclude others**, take a lock per document with a time to live.
- **When a write can be replayed**, make the write itself idempotent and test the replay: transaction identifiers scoped to the checkpoint, update-only merges (idempotent, never inserting or changing membership), `[start, end)` windows with a settlement lag, uploads deduplicated by content hash. Delivery is at-least-once; the consumer is idempotent.
- **When two writes can fail between them**, order them so the next run self-heals, and write the order down: that order duplicates rows rather than losing them. Prefer a duplicate to an incompleteness and an incompleteness to a loss.
- **When a cache is per request**, bind it per request (it keeps `self` out of the key).
- **When a callback feeds a queue**, bound the queue, drop on full, log the drop at most once per interval with a monotonic clock, warn at half full, and block with a short timeout, never a sleep: a sleep throttles the consumer to one batch per interval.
- **When an operation can outlive the caller's timeout**, return immediately with values derivable from the inputs, do the slow work in a background thread that must never raise, and let the caller poll observable state. Prefer no new server-side state, even at the cost of an explicit failure signal.
- **When calling the cloud from an unreliable network**, retry with bounded exponential backoff and jitter, log each retry, reuse one client per batch, and let the storage SDK own block-level retries; stacking them would force full re-uploads and stack backoff past the validity window of a signed upload URL.
- **Fan-out reads settle together**: gather all, then re-raise the first failure.

## In practice: Python services

Applies when the repository uses asyncio.

- Everything is `async def`; a synchronous SDK runs in `asyncio.to_thread`; fan-out is `gather(..., return_exceptions=True)`.
