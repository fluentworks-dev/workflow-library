Push the branch, never by force. Open one draft pull request per repository. The title is the subject that will land on the default branch: one sentence naming the effect for the person or the consumer, in the repository's convention.

When the repository has a pull request template, fill that template and add what it lacks: the test plan with counts, the related pull requests. Otherwise use this one:

```
## Changes
- `<symbol or file>`: <the behavior now>, with the reason folded into the sentence ("so ...")
- **Breaking**: <what a consumer must change>            (only when there is one)
- Behavior change in tests: <which assertion changed, why> (only when there is one)

## Test plan
- [ ] `<exact command>`                     (Validation fills the count and the baseline)
- [ ] <live row from code-design.md>: <what to call, compare and expect>   (Validation runs it)
- [ ] <what only the person can run, with the exact step>

## Rollout            (only when order matters or a person must act)
<what to do before and after the merge, each step with the condition that proves it
 worked; what a rollback would lose when it is not the mirror of rolling forward>

## Related PRs        (only when more than one repository is involved)
- Requires: [<repository> #N](<url>) <its state>
- Required by: [<repository> #N](<url>)
- Merge order: <first> #N, then this pull request, then <last> #N

## Decided for you
- <a choice the designs left open>: <what was chosen and why, in one line>

<the generator footer your session adds; never strip it>
```

Rules for the description:

- It says what changed. The reason rides inside each sentence; there is no separate section for it.
- A number you write is one you saw; the gate's counts and their baselines are written by Validation, in the form "126 passed, 1 skipped (baseline before this branch: 114 passed, 1 skipped)".
- Failures and gaps are stated, not hidden: "the 30 failures in `date-picker`, `checkout-form` and `session-warning-dialog` reproduce identically on the unmodified parent commit".
- A temporary pin to an unreleased build is flagged in bold ("**Swap this for a released SDK version before merging.**") with an unchecked item in the test plan.
- A screenshot appears only for a change a person sees, above the first heading.
- A section is named for what its reader must do: `## Before merging`, `## Post-merge steps (reviewer to run)`, `## Deployment note`, or `## Review guide` when most files only carry a rename. A cost or a risk the reviewer should weigh is a "Reviewer note:" line.
- It describes nothing the branch does not contain. Reread every sentence against the final diff after the last commit.

A filled example:

```
Title: Accept an association name on file attach [TICKET-123]

## Changes
- `AssociationNameField` in `src/models/requests/validated_fields.py`: one annotated type that
  rejects whitespace-only and over-long names, so the rule holds for every client and not only
  for the upload form.
- `AttachFileRequestV2.association_name`: optional. A blank value is left out of the upstream
  call entirely, so the upstream service applies its own default.
- `attach_file_v2` forwards the name under the keyword the generated client expects; the route
  test asserts the exact body class that is sent.

## Test plan
- [ ] `poetry run pytest tests/` (Validation: count and baseline)
- [ ] `pre-commit run --all-files` (Validation: count and baseline)
- [x] Mutation check: removing `exclude_none=True` makes `test_attach_file_omits_a_blank_association_name` fail
- [ ] Development environment: attach three files through the generated client, named, blank, whitespace-only; expect 201, 201 with the upstream default name, 422 (Validation)
- [ ] Staging: attach from the web application once its pull request has merged

## Related PRs
- Required by: [web-application, the upload form](https://example.com/web-application/pull/upload-form) (needs the TypeScript client this pull request publishes on merge)
- Merge order: this pull request, then the web-application one

## Decided for you
- The 422 message reads "association_name must not be blank": the design left the wording open.
- The name is trimmed before its length is checked, as `NameField` already does.

🤖 Generated with [Claude Code](https://claude.com/claude-code)
```

Attach the pull request to the ticket. Your summary says what was built in one line and links it.

### Questions to ask here

- Does the description say anything the branch does not do?
- Where is the baseline count?
- Which boxes are unchecked, and who checks them?
- Is there a development pin that must be swapped before merge?
- Does someone have to run something after this merges? Where is that written?
- One pull request per repository? What is the merge order?
