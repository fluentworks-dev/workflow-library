Write design-review.md, compare it with the example below, and save it with the revised designs on the ticket. It records what the review did; do not ask the person to approve it.

```
# Design review: <ticket title>

Status: revision <n>. Reviewed scope.md (revision <n>), product-design.md (revision <n>), system-design.md (revision <n> | none: the board has no System Design column), code-design.md (revision <n>).
Verdict: Ready to build | Not ready to build: <the one reason>
Waits for: nothing | <each change still unmerged, its ticket, and whether this ticket is now blocked by it>

## What the designs commit to
<Three to six lines in plain words: what will be built, as the review understood it.>

## Findings
| # | Where | Severity | Finding | What changed, or why it stands |

## Simpler forms considered
| Simpler form | Removes | Gives up | Outcome |

## Facts checked
| Claim a design relies on | Checked against | Result |

## Lenses
<One entry per lens, "clean" or the finding numbers: need; product; data and contracts; structure and configuration; failure and concurrency; security; cost and performance; operation and rollout; code design; consistency; blind spots.>

## Documents revised
<Document, new revision, what changed in one line. Or "None".>

## Decisions
| Decision | Choice | Reason | Decided by |
<each answer the person gave to this review's questions, in their terms. Or "None".>

## Decided for you
<Choices this review made or changed, one line each with what it gives up; first, at most five the person should read first. Or "None".>

## Follow-ups
<Real findings outside this ticket, one line each. Or "None".>

## Not checked
<What you could not verify or did not judge, why, and who should. Or "None".>
```

**Rules.**

- "Ready to build" never appears above an open blocker.
- Every section stays; an empty one says "None".
- Every finding says where it is (the document and the section) and ends in what changed, or in what you checked and why the design stands.
- The security lens always has an entry; when it is clean, the entry says what was walked.
- Write for the person reading on a phone without the designs open: full words, one term per concept, exact numbers.
- Paste nothing from another tool unless you verified it. A summary that says only "Looks good" is not a review.
- Reread it against the revised documents: a review that describes a fix the documents do not contain is the first blind spot.

**The three-line form,** when there was nothing to review:

```
# Design review: <ticket title>
Reviewed scope.md and the design documents. Each says there was nothing to design, and the code agrees: <the reason in one line>.
Verdict: Ready to build. No findings.
```

**A filled example.** The change lets a person choose or type an association name for each uploaded file, across a web application, its backend and an asset service.

```
# Design review: choose an association name for each uploaded file

Status: revision 1. Reviewed scope.md (revision 2), product-design.md (revision 1), system-design.md (revision 1, now 2), code-design.md (revision 1, now 2).
Verdict: Ready to build.
Waits for: nothing.

## What the designs commit to
A person uploading files picks a suggested association name or types one, per file. The backend passes the name to the asset service, which already accepts it. A blank name is sent as no name at all, so the asset service applies its own default. No stored data changes.

## Findings
| # | Where | Severity | Finding | What changed, or why it stands |
|---|---|---|---|---|
| 1 | system-design.md, Contracts | Blocker | The request sent a blank name as `""`; product-design.md says blank means not given. Verified against the installed client 0.5.3: the field has a minimum length of 1, so `""` is rejected with 422. | Fixed in revision 2: the key is omitted when blank. code-design.md task 2 gained `test_stage_file_omits_a_blank_association_name`. |
| 2 | code-design.md, Concepts | Simplification | `AssociationNameResolver` is a class with one method and one caller. | Fixed in revision 2: inlined into the route. Removes one file, one class and one test file. |
| 3 | system-design.md, Stored data | Simplification | A stored `name_source` that nothing reads. Can probably drop it for now. | Fixed in revision 2: removed, and named under Seams. |
| 4 | code-design.md, Tasks | Blocker | The backend pins development build `0.0.1.dev97` of the client and no task swaps it for a release. | Fixed in revision 2: the swap is the last task, with its unchecked test-plan item. |
| 5 | code-design.md, Interfaces | Simplification | The suggestion list copies the service's enumeration. Could we import it from the generated client instead? | Stands. The copy is deliberate, so a new name in the service does not force a release of the web application. It carries a source-of-truth comment. |

## Simpler forms considered
| Simpler form | Removes | Gives up | Outcome |
|---|---|---|---|
| Suggestions only, no typing | The text input and its validation | Names the list does not have yet | Not taken: outcome 2 in scope.md requires typing |
| No resolver class | `association_name_resolver.py`, its class, its test file | Nothing | Taken (finding 2) |

## Facts checked
| Claim a design relies on | Checked against | Result |
|---|---|---|
| The asset service already accepts an association name on attach | Installed client 0.5.3, the attach request model | True |
| No other consumer sends this field | Search of the three sibling repositories | True, 0 matches |

## Lenses
Need: 2, 3. Product: clean. Data and contracts: 1. Structure and configuration: clean. Failure and concurrency: clean. Security: clean; the name is free text other people see, rendered as text and limited to 80 characters by the service. Cost and performance: clean, no new paid path. Operation and rollout: 4. Code design: 2, 5 (stands). Consistency: 1. Blind spots: 4 (release tail); the rest clean.

## Documents revised
- system-design.md, revision 2 (replaces revision 1): blank name omitted; `name_source` removed and named as a seam.
- code-design.md, revision 2 (replaces revision 1): resolver inlined; blank-name test added to task 2; release swap added as the last task.

## Decisions
None: nothing in this review was the person's.

## Decided for you
- The suggestions stay a list copied into the web application, and the asset service stays the only place that decides which names are valid. Gives up a list that is always current: a name added to the service can be typed at once but is suggested only after the web application is updated. Fetching it would cost one new route and one request each time the dialog opens.
- Inlined the resolver: a class for one caller adds a file and no meaning.

## Follow-ups
None.

## Not checked
The look of the suggestion chips beyond tokens and spacing; the product designer's file is the reference.
```

**Hand on.** Your summary says in one line whether the design is ready to build and what changed, and links design-review.md.

**Questions to ask here**

- Is it ready to build: yes or no?
- What does the person have to know, in five lines or fewer?
- Is every "Fixed in revision 2" really in revision 2?
- Did anything in here not get checked? Then it goes under "Not checked".
