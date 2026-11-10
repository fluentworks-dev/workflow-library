Write the examples first: two or three numbered, concrete examples of the result (a request and its response, a row before and after, a file as it will be written, a configuration block). If writing a schema needs more than the examples in this document, the design has failed.

Then specify, in this order.

**Components and processes.** For each one added, changed or removed: its one job, who calls it, what it calls, and where each rule is enforced. A new process says how it starts, how it stops, what happens when two copies run, what it holds in memory that a restart loses, and how its health is known.

An entry point maps failures through one domain exception hierarchy that carries the status codes. It never chooses a status by reading an error's text. The shape to avoid:

```python
    except Exception as e:
        error_msg = str(e).lower()
        if "not found" in error_msg:
            raise HTTPException(status_code=404, detail=str(e)) from e
        else:
            raise HTTPException(status_code=500, detail="Internal server error") from e
```

The contract to write instead, in shape: one manager call, a domain exception passed on unchanged, anything else logged and answered as 500.

```python
    try:
        return await manager.get_project(project_id)
    except DomainHTTPException:
        raise
    except Exception as error:
        logger.error("get_project failed", exc_info=True)
        raise HTTPException(status_code=500, detail="Internal server error") from error
```

**Stored data.** For each store, table, document or file:

- the shape as a schema or a type, written as code, with the access patterns it serves: each read and write with its key, and an index for each query;
- what is the source and what is derived (a derived value is computed, not stored, unless the cost stage justifies storing it);
- how an invalid state is made impossible: required discriminators, closed sets, uniqueness as a constraint in the store, required values with no silent default;
- a reversible change of state (a status field or a soft delete) before a hard delete;
- indexes, declared as data and applied at deploy time;
- retention, as a number with a reason and the mechanism that enforces it;
- each change classified, conservatively: anything that can make an existing reader fail is breaking.

| Change | Class | Why |
|---|---|---|
| New nullable column `exports.expires_at` | Additive | Old readers ignore it |
| `status` gains the value `expired` | Breaking for consumers | A closed enum is visible to consumers: publish the SDK, upgrade the consumers, then emit the value |

**Contracts.** For each interface, event, file or configuration name another party relies on:

- examples of use first;
- its behavior per version: a new meaning is a new version, and the old version keeps its behavior and its status codes. Stated semantics read like this: v1, PUT replaces and the version tag travels in the body; v2, PATCH with typed patches, the tag in the ETag header, 201 on creates; v3, discriminated unions, `If-Match`, and `created_at` as the default sort so that offset and limit paging does not skip or duplicate;
- every error a consumer can receive, with status, sentence and the consumer's next action. An error with no next action is a gap;
- the consumers you found while grounding, and what each sees before, during and after.

When one service passes another's errors on, the design names which statuses pass through, and the reason for an exclusion is written at the constant. Everything else answers 502 with a request identifier and no upstream detail.

```python
# Statuses whose body carries a user-actionable message from the upstream service
# (invalid link, missing target, referenced record, upstream validation,
# tag-based access denial). 401 is deliberately absent: it means our
# on-behalf-of token exchange failed, which is our problem, not the user's.
_PASSTHROUGH_STATUSES = {400, 403, 404, 409, 422}
```

At a product boundary for non-technical users, an error is a structure, not a string: the domain it belongs to, a message and a remediation. "The name 'X' is already taken by another app." goes with "Pick a different app name." The rule on the type reads "Never let raw exceptions reach the client."; an unexpected exception is redacted for the user and audited as one line of JSON.

**Infrastructure and configuration.**

- each resource with its name, its size, its monthly price and the reason for the size, its network exposure (private unless a consumer outside needs it), its region and compliance boundary, the identity that reaches it, and who applies it;
- each new configuration value marked "selects an environment" (required, no default in code) or "tunes behavior" (optional, documented default with a unit);
- every place a new variable must be added, as a list;
- each secret: who creates it, how it is delivered, how it rotates. Prefer identity that needs no stored secret;
- what is validated at startup, and the message it fails with.

A startup check fails fast, and its message explains the constraint it enforces:

```python
        if len(hierarchy) != 1:
            raise ConfigurationError(
                "the document store backend requires orders, order lines, and shipments "
                "to map to one container: creating an order line or a shipment links it "
                "to its parent inside one transactional batch"
            )
```

**Compatibility plan.** For every breaking change: the expand step, the consumer moves, the contract step as a later change. Prefer a read-time fallback to a backfill; when a backfill is unavoidable, specify an idempotent script with a dry run and bounded concurrency. A migration ships with code only when the running system can serve before, during and after it:

| Schema change | Ships with the code that uses it |
|---|---|
| New table, new nullable column, column with a constant default, index on a small table | Yes, when the migration is applied before the new code serves a request |
| Index on a large table | No: its own step, built without blocking writes |
| New required column, tightened constraint, type change | No: add nullable or beside the old, backfill or fall back on read, tighten or remove in a later change |
| Rename or drop of anything the running system still reads | Never: the code that stops reading it deploys first, and the drop follows in a later pull request |

To retire a contract: mark it deprecated in the contract itself with what replaces it; add a test that every operation of the deprecated version carries the mark; list every caller found in the consumer repositories; file a ticket for each consumer; remove it in a change of its own after the last consumer has released without it.

**Decide without asking, and record:** additive nullable columns and tables, names, defaults, limits, indexes, error wording, the number of a new API version, and where a list is filtered, sorted and paged: in the database, unless it cannot or there is a good reason not to, which you write down. **Ask**, in the session's batch, only about a destructive change to stored data, or a contract change that forces work on a consumer the person does not own.

## Questions to ask here

- can this be non-nullable?
- can the `id` be dropped, since the structure already gives it?
- can this list be a map, keyed by the name?
- does any new environment variable need to be added to the infrastructure definitions?
- does this script need to be stored and committed now, or can it go?
- is this list filtered, sorted and paged in the database? if not, what stops it?
- is this stored anywhere else already? which one is the source?
- what can be written here that should be impossible?
- who breaks if this field goes away?
