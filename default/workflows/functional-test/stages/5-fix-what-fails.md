Fix what you found, on this branch: failures from the run and the probes, gaps against the designs, and small problems nearby that already existed, when the fix is in this repository and under about two hours. Do not file a follow-up for a clear fix.

For each defect, in order:

1. Write the regression test from the real case: the real object type, the real input that broke, the failure story in the docstring. Run it and see it fail for the right reason.
2. Make the smallest fix that is correct, in the repository's style. Every changed line traces to the defect.
3. Run the test green. For a defect that matters, break the fix once and watch the test fail again, and say so: "Verified it fails with the offset reset removed from the runs handler and passes with it restored".
4. Rerun the route or probe that failed, and the checks related to the files you touched.
5. Commit in the repository's convention. Where it has none: a subject that names the effect, and a body that states the failure, the cause, the change and what you measured. A fix for something that already failed on the default branch gets its own commit.
6. Push without force. After an approval, sweep the affected routes once more and announce any commit you add, in this form: "New commit since approval: `<commit>`, fixing four issues a full endpoint sweep against dev found. All four also exist on main."
7. When the defect came from a reviewer or a review bot, answer the thread with the fixing commit, the covering test and the mutation check: "Good catch, fixed in `<commit>` ... Covered by `test_create_order_with_an_unreadable_payment_method_returns_400_and_writes_no_order` and two manager tests."; "Fixed in `<commit>` ... Covered by 'reports a duplicate key even when another row is still blank'".

Name the test as the behavior sentence a failing run should print:

- `test_update_order_sets_parent_guards_write_with_etag`
- `test_get_invoice_keeps_its_own_404_text_for_not_found`
- `test_stage_file_requests_the_upload_url_under_its_association_name`
- `test_no_empty_trailing_file_when_run_ends_at_cap`
- `refuses a value too large to be read back rather than truncating it`
- `keeps every cookie under the browser limit for a claims-heavy session`

Name a helper for what it builds: `sessionExpiringIn`, `keepaliveRequestFor`, `minimal_valid_payload`.

When an existing test now fails: if a design says the behavior changes, update the test in the same commit and add a "Behavior change in tests:" paragraph, because a change that alters what a test asserts (a behavioral change) is called out in the commit message or the pull request. If not, the test is right and the code is wrong. When a plan says not to modify existing tests, obey it: if an existing test breaks, stop and report it rather than editing it. When the fix touches a legacy suite that no longer runs, translate its cases into the suite that runs, delete it and say so in the description. Never loosen, skip or regenerate a check to get green. After three failed attempts at one problem, stop: the approach is wrong, so reread the design.

Where the repository pairs each test with a markdown specification, a new test is a pair of the same name:

```markdown
## effective_value with order-level document returns order value when set

When `effective_value` is called on a document with order-level settings `{gift_wrap: "false"}` and account-level settings `{gift_wrap: "true"}`, looking for key `gift_wrap` in the order entity level shape `(settings, customer_settings, store_settings, account_settings)`.

Expect the returned value is `"false"`, showing that nearest-level override beats the account-level value.
```

```python
def test_effective_value_with_order_level_document_returns_order_value_when_set():
    # Given
    doc = {"settings": {"gift_wrap": "false"}, "customer_settings": None, "store_settings": None,
           "account_settings": {"gift_wrap": "true"}}
    levels = ENTITY_SETTING_FIELDS["order"]

    # When
    result = effective_value(doc, "gift_wrap", levels)

    # Expect
    assert result == "false"
```

Settled, and not asked: a difference from a design that follows from a decision the person made (note it as a deliberate divergence), and a step only a person can take (write it as an exact step for the summary).

Ask the person with AskUserQuestion once, after testing, one question per finding, written as the shared rule on how to ask says (what a person or a caller would meet, in plain words, and the result of what you ran, not the run), your recommendation first, only when a problem cannot be fixed here, or the fix would change behavior others rely on, touch shared data or undo a decision the person made. A fix with no clear winner is yours to choose. The shape, with an invented finding:

```
Question:  Apps on the previous release send a blank name in a form this branch now refuses,
           and three services still use that release. What should happen to them?
Option A:  "Accept the old form (Recommended)". Old apps keep working; nothing changes for new ones.
Option B:  "Upgrade the three services first". The merge waits for three pull requests.
Option C:  "Merge now". The three services fail on blank names until they upgrade.
```

A fixing commit: a subject naming the effect, a body with the failure, the cause, the change and the measurement, then the paragraph on tests.

```
Refuse an association name longer than 80 characters instead of truncating it

The attach route passed the name to a helper that cut it to 80
characters, so a name of 81 characters was stored shortened and the
caller got 200. The limit is now enforced by the request type, the
route answers 422 with the server's sentence, and nothing is written.

Behavior change in tests: test_attach_file_trims_a_long_name asserted
the truncation and now asserts the refusal under a new name.
```

### Questions to ask here

- Does the test fail without the fix? "Confirmed failing on the parent commit and passing here."
- Is the test built from the real thing that broke?
- Can it be fixed without adding new machinery?
- Is any change here not relevant to the defect?
- Did the fix change what another test asserts? Then say so in the commit.
- Did the person already decide this? Then leave it.
