Do this for every design that adds or changes a boundary (a route, a queue, a file drop, a webhook), a data flow, a credential, or who can see or change something. When the design has none of these, write "Security: no new boundary, data flow or credential" and go to the next stage.

**List the elements**: each new or changed entry point, data store, data flow and credential, and who can reach it.

**Ask STRIDE of each element** (spoofing, tampering, repudiation, information disclosure, denial of service, elevation of privilege) and write one row per threat that applies. Of each element ask: who can call this, what do they control, what happens if they lie.

| Element | Category | Threat | Answer |
|---|---|---|---|
| `GET /v2/exports/{id}/download` | Information disclosure | A user guesses the identifier of another project's export | The manager checks the requester's policy against the export's project; a denial answers 404, the same as a missing export |
| Signed download URL | Information disclosure | The URL is forwarded to someone else | It expires after 5 minutes, is issued only after the access check, and is never logged |
| Export worker role | Elevation of privilege | The worker can read every bucket | The role is scoped to the exports prefix, read and write only |
| `POST /v2/exports` | Denial of service | A loop requests exports | One running export per project and requester; a second request returns the running one |

**Check the standing positions** against the design:

- One authority decides identity, on the server. No comparison of identity strings from two sources.
- No credential reaches a browser or a device. No service credential stands in for a user's action.
- Identity is proven without a stored secret where the platform allows it. Where a secret exists: who creates it, where it lives, how it rotates.
- Each actor has the least privilege that works. A pipeline that runs code from a pull request holds no write credential.
- A missing identity fails closed.
- A user-supplied value reaches a query only as a parameter; identifiers are validated against a pattern.
- Logs carry identifiers only. Name the test that searches the captured log.
- Each component sits inside a named compliance boundary, and every service it uses is available there.
- Nothing insecure is left dormant: a path the design replaces is deleted. An unused login route that is public and mints a self-signed token lets any request carrying an arbitrary token pass the gate; the route, its helper and its user store go in one commit.

**Every finding gets an answer in the document**: closed by the design, or accepted. An accepted gap is a row in Decisions, decided by the person, with the condition that closes it. Put a gap you cannot close inside this ticket in the session's batch. Never ship one as a note: that is how an unauthenticated endpoint that lists users, or a secret delivered as a plain application setting, ships, and how a high-severity finding that nobody answered gets copied into a second repository.

## Questions to ask here

- who can call this, what do they control, what happens if they lie?
- does a denied read tell them the thing exists?
- does this work in every region and cloud partition the product runs in?
- is there a secret here at all, or can the platform prove who we are?
- what does this log? any value in there, or identifiers only?
- can code from a pull request reach this credential?
