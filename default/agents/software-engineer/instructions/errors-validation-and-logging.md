- When code can detect a bad input, fail at the earliest layer, with a message naming the rule and the value (`"export_format must be one of {EXPORT_FORMATS}, got {export_format!r}"`): failing early and fast saves the work, such as a workflow activity call, that a later failure would waste. Validate configuration at startup, never mid-run, and reject unknown keys: typos must surface rather than being silently ignored.
- When a case is abnormal (the file exists, the scheme is unsupported, the value is too large), raise. Never tolerate, overwrite or truncate: an error makes someone look at what is happening.
- When a timeout or a wait is part of normal operation, it is control flow, not an error: fail the workflow only when there is a bug a developer must look at and fix.
- When a process must survive (a node, a user-defined function, a background thread, the last-resort 500 path), catch broadly there and only there, log with context, and return a visible failure. Never let a cloud failure crash a node or block a run, except where continuing would silently detach the work from its record; then fail, and write the exception down.
- When a batch partially fails, report each failed item with its error and set success to false (`success = not failed_files`).
- When a composed read hits one corrupt part, degrade that part and log. When a direct read hits it, surface the error.
- When raising, use domain exceptions with concrete messages and `from error`; argument errors are for bad arguments only. Never choose a response by matching exception text. At a product boundary, never let raw exceptions reach the client.
- When placing validation: shape in the model at the boundary; a cross-field rule in the layer that has the context; nothing an upstream service already enforces (rely on the upstream service to enforce it). Delete a validator that can never run.

Logging:

- When logging, use the module's logger and the library's deferred formatting where it has one, `exc_info` on 500 paths, warning for an upstream 4xx and error for a 5xx.
- When a secret or a sensitive value could reach a log, log identifiers only (a secret's name, never its value) and write a test that searches the captured log.
- When a fault recurs, log it at most once per interval, with a monotonic clock; log a data-shape failure once per batch rather than once per record. Log each retry.
- When warning an operator, use the platform's logger. At a product boundary show a domain, a message and a remediation, never a stack trace.
