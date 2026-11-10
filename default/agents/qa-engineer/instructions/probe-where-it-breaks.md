After the expected path works, try to break it. The seven questions, asked of every step a person or caller takes, mean:

- **Nothing**: empty input, no data yet, a blank optional field, a missing file.
- **Many**: large volumes, long values, more than one page, many callers at once.
- **Wrong**: invalid, malformed or hostile input; a typo in configuration; a wrong type at a known path.
- **Broken**: a dependency fails, is slow or answers with an error; the network drops midway; the disk fills.
- **Again**: repeated, replayed, retried, clicked twice, two callers at the same moment.
- **Back**: undo, leave and return, upgrade and roll back, data stored by an older version.
- **Someone else**: another user, another permission level, another tenant, no permission.

Probes the usual list misses:

- **Limits** worth knowing: a name of 1 to 80 characters after trimming, a cookie of 4,096 bytes, a freshness window. A stored version that carries a retired field stays readable.
- **Refusals** happen at the earliest layer, with a message a person can act on. An abnormal case raises; it is never tolerated, overwritten or truncated.
- **Success that cannot fail**: make the operation fail and see the success flag turn false and each retry logged. An always-true flag and a silent retry are recurring defects.
- **Access and injection, even when no design asks**: security is easy to miss in review, so it is a standing probe here. On any change that touches identity, access, secrets or a query, call it with no credentials, with another person's and with a lower level, and put hostile text into every value that reaches a query, a path or a shell. A missing check is a blocker to report, never a note.
- **Time**: clock skew, a record dated in the future, both edges of a window, a token that expires mid-request.
- **Parallel surfaces**: would a person still meet the old behavior on another screen, in another client, in the documentation?

Use the named techniques where they help: risk-based testing, equivalence classes and boundary values, state transitions (every allowed move and one refused move per state), decision tables, property checks, contract testing, exploratory testing, fault injection, the OWASP Top 10.
