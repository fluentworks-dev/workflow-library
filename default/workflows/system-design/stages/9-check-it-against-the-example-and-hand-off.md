A filled example, abridged, in the voice to use:

```
Status: draft, revision 2 (revision 1 kept export files in the database; superseded after the cost estimate)

## Intent
A project lead who needs a project's files outside the application downloads them one at a time; a project of 300 files takes an afternoon. The insight: an export is a file with a lifetime, not a new kind of record, so it needs one table and one bucket prefix and no new service.

## Goals
1. A user requests an export of one project and downloads one archive.
2. The request answers in under 1 second; the archive is ready within 5 minutes for a 2 GB project (seed project, measured: 2 min 10 s).
3. Nobody downloads an export of a project they cannot read.

## Non-goals
Scheduled exports. Exports across projects. Any change to the upload path.

## Anti-goals
- A second copy of project files that outlives 7 days.
- An export status stored in two places.
- A download URL in any log.

## Constraints
- Migrations are applied by the deploy before the new version serves (`deploy/migrate.sh:12`, verified on `origin/main`).
- Assumption: the largest project is under 5 GB. Depends on the storage report of 2026-11-01.

## What it looks like
1. `POST /v2/projects/{project_id}/exports` answers 202 with `{"id": "...", "status": "running"}`.
2. `GET /v2/exports/{id}` answers `{"status": "ready", "expires_at": "2026-11-11T18:00:00+00:00"}`.
3. `GET /v2/exports/{id}/download` answers 302 to a signed URL valid for 5 minutes.

## The model
### Stored data
`exports(id, project_id, requester_id, request_key, status, expires_at, created_at)`, unique on `(project_id, request_key)`. Additive: a new table. The object key is derived from `id` and is not stored.
### Contracts
New routes only, in v2. A denied or missing export answers 404. `status` is a closed set: `running`, `ready`, `failed`, `expired`.
[The other parts of the model, and the sections Failure and concurrency through Operation, hold the tables from the earlier stages.]

## Decisions
| Decision | Choice | Reason | Decided by |
| Who can open a download link | Only the person who asked, signed in | No file reaches anyone without access to the project | <the person's name> |
| Where files live | One private bucket in the existing account, about 9 USD a month at 100 organizations | Files in the database grow every backup by the size of every export | System Design |
| How the worker runs | A task in the existing process | One export at a time per project needs no new deployable | System Design |
| How the interface learns it is ready | Poll every 5 seconds while the dialog is open | No new server-side state; the poll stops when the status is final | System Design |

### Where files live, in long form
Context: an export is 50 MB to 5 GB, and the database is backed up in full every night.
Recommendation: the bucket. It wins because storage tracks the last 7 days and backups do not grow. The database lost on backup size; building on every download lost on a 40 to 90 second wait. Strongest objection: one more resource to apply and watch. Answer: it holds nothing that cannot be rebuilt, and the expiry rule is its only moving part. Revisit if an export must live longer than 30 days.

## Decided for you
- Expiry is 7 days. Chosen by feel: it survives a weekend and keeps storage tied to recent use.
- A second request while one is running returns the running export.

## Seams
- Scheduled exports are not built. A schedule would insert the same `exports` row; nothing may assume a user requested it.

## Done means
- A replayed request with the same `request_key` creates no second row.
- A requester without read access gets 404 on status and on download.
- The captured log of a full export contains the export identifier and no URL.

## Left to the builder
The archive library. How the worker batches files.

## Testing and rollout
The rollout table. Steps for the person: 1. apply the bucket and the role from the branch; a second plan reports "No changes".

## Open questions for Code Design
Whether the worker task uses the request's store instances or its own from the wiring module.
```

Before saving, reread your document against this list and fix what fails:

1. No two sections contradict each other, and no sentence can be read two ways.
2. Every contract change lists its consumers. Every breaking change has an expand step, a consumer move and a later contract step.
3. Every write has a row in the failure table.
4. Every threat that applies has an answer. No gap is only a note.
5. Every price has a source and a date.
6. Every step a person must run is numbered and exact and has a proving condition.
7. Nothing in the design is larger than the scope. Something larger that you found is one line for a new ticket.
8. The version with one concept fewer was considered, and the document says why it lost, or it won.
9. The names in the document match the repository's terms and each other.
10. Every sentence describes the final design, not an earlier revision of it.

Do not ask for approval: the document records decisions the person made and choices they can overturn.

Your summary is a few lines: the design in one sentence, the decisions the person made, and what a person must do before or after the merge. Then one link to system-design.md. Example:

> Project exports are one new table and one private bucket with a 7-day expiry, served by a task in the existing process; nothing existing changes. You chose that only the person who asked can open the download link. Before the merge you apply the bucket and the role from the branch.

## Questions to ask here

- does the document still describe something the design dropped?
- what did we decide without asking, and will the person see it?
- is anything in here bigger than the ticket?
- could this be one concept fewer?
