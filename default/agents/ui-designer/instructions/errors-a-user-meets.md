Error behavior is designed, not defaulted. For each failure decide whether it is shown, where, in which words, and what the user does next.

| The failure | Who can act | What the user sees |
|---|---|---|
| The request is wrong: invalid, already exists, changed by someone else, still referenced | The user | The system's own sentence, unchanged, under a heading that names the class of failure |
| The user is not allowed | The user or their administrator | The system's sentence. It names the key that blocks them, never its value |
| The thing does not exist, or the user may not read it | Nobody here | The same sentence for both: "This item does not exist or you do not have access to it." |
| The system failed: an internal error, a timeout, bad configuration, the product's own sign-in to another service | The team | "The server ran into a problem. Try again, and contact support if it keeps happening." with a request id. Never the internal detail |

- When an error is one the user can act on, show the system's own sentence and carry its status and detail unchanged through every layer, because the service that owns the rule owns the wording and a reworded copy drifts.
- When the failure is the system's own, never show its detail. Return a general failure with a request id the user can quote and the team can search for; the detail goes to the log. The request id goes beyond common practice; keep it.
- When the product's own sign-in to another service fails, treat it as the system's failure, not the user's. Never send the user to a sign-in that cannot fix it.
- When a denial would reveal that the thing exists, answer as if it does not exist, matching every other single-document access in the API. This is the OWASP guidance on not revealing existence.
- When a write would remove the user's own access, refuse it as a permission problem, so clients can tell a permission problem from a validation problem, naming the keys and never their values.
- Tell a conflict as a conflict (someone saved first, the name is taken, the thing is still referenced), with what to do about it.

**What an error says**

- When showing an error, write it for the user's next action: what happened in their terms, whether their work was kept, what to do now. The heading names the class of failure.
- A message stands alone: every denial opens with a fixed prefix such as "Request denied:" because clients may show it without any surrounding heading.
- When the user can fix the input, name the rule and the value: `"retry_policy must be one of {RETRY_POLICIES}, got {retry_policy!r}"`. For a missing thing, name the kind and the id: `"{target_type} with id {target_id} not found."`
- A message never contains a secret or a sensitive value, on a screen, in a log or in an email. Keys are fine; values are not.
- Build messages in one place, so the same failure reads the same everywhere and a rewording is one edit.
- Error wording is a contract: consumers copy an upstream service's sentences word for word into their tests and copy them again on every rewording. Settle the sentence in the design.
- One `detail` sentence is enough. Typed error descriptions per status add little value and clutter the documentation page. RFC 9457 problem details is the wider standard; adopt it when a consumer needs fields a program can read.
- Tell a failure at the first step that can still be fixed. A session too large to store is refused with the numbers and the fix, because truncating it reads as an endless login redirect with nothing in the logs:

```ts
throw new Error(
  `Sealed session needs ${chunks.length} cookies but only ${MAX_SESSION_CHUNKS} are read ` +
    `back (${sealed.length} bytes at ${SESSION_CHUNK_SIZE_BYTES} bytes per cookie). Raise ` +
    `MAX_SESSION_CHUNKS or store less in sealSession.`
);
```

### In practice: REST services

Applies when the repository uses a REST service (FastAPI or similar).

- Failures fall into classes by status code: 400 invalid; 401 and 403 access; 404 not found; 409 already exists, concurrency conflict or referential integrity; 412 precondition failed; 500 internal, configuration and data integrity. Design one message per class, not one per endpoint.
- The user sees the detail of a 4xx, because they can act on it, and never the detail of a 5xx.
- A status code is frozen per API version: legacy clients depend on it, so do not fix it.
- Choose what a user sees by the class of the failure, never by searching the exception's text for "not found".
