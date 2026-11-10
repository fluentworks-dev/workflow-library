Fill one row for each write the design adds or changes. A write is anything that changes state: a row, a document, a file, a message, a call to another system that has an effect.

| Write | Runs twice | Stops halfway | Two actors at once | Left behind |
|---|---|---|---|---|
| Insert the `exports` row | The second insert hits the unique key `(project_id, request_key)` and returns the existing row | Nothing is written | Same key, one row | Nothing |
| Upload the file, then set `status = ready` | The upload overwrites the same object key; the update changes nothing | The file exists and the row is still `running`; the next worker pass redoes any row `running` for more than 15 minutes | The worker claims the row with a version check; the loser skips | An object with no `ready` row, removed by the 7-day expiry |

For each row decide:

- **Idempotency**: the key that makes a repeat harmless, or the version check that rejects it.
- **Order**, when two writes cannot be atomic: the order in which a crash between them leaves a duplicate or a visible incompleteness, never a silent loss. Write the order and its reason as a sentence the builder will copy into a comment, in this register: "Write the line items before the order summary so a failure between the two writes leaves the summary without `is_complete=True`, making the next run self-healing". For the example: "Upload first, then set `ready`, so a crash leaves a file nobody can see, never a `ready` row with no file."
- **Concurrency**: a version tag threaded from the read to the write; one transactional batch for a parent and its child; a separate key per actor before any lock; anything a run leaves behind (a lock, a temporary file, a claimed job) recognizable as stale.

Then answer for the design as a whole:

- **Delivery semantics** of every queue, stream, webhook and schedule: at-least-once with an idempotent consumer, and what the consumer deduplicates on. For a request a consumer may retry, an idempotency key. When a database write and a message must both happen, an outbox table in the same transaction.
- **What is kept when processing fails**: the input as received, where it goes, and with what reason.
- **Each queue and buffer**: its bound, what happens when it is full, and how a drop is reported.
- **Each remote call**: its timeout, whether it is retried, with what backoff, and which one layer owns the retry.
- **Each long operation**: whether it can outlive the caller's timeout. If it can, it returns at once and exposes state to poll.
- **Where failure stops**: which process must survive and so catches at its boundary; which failures must stop a start (bad configuration, missing wiring); which waits are normal flow.
- **What a restart does**: each step is derived from the state that exists, not from what the last run meant to do.

Name the test that proves each replay and each ordering, as a sentence: "a replayed request with the same key creates no second row".

Decide all of this yourself. Put a point in the session's batch only when a failure mode forces a product choice that the scope did not make, such as whether a user may lose an edit in progress or must be told to refresh.

## Questions to ask here

- what happens if this runs twice?
- what is left behind if it dies between these two writes? does the next run fix it?
- is it better to error out here, so that someone can look at what is happening?
- does a timeout here mean something is broken, or is it only a wait?
- does a failed upload block the run? should it?
- who retries, us or the SDK? not both.
- what happens when the queue is full?
