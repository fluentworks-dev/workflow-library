Post one validation summary comment on the pull request. That comment is the record. Keep every section and write "None" where there is nothing. The sections, in order:

1. **Result**: one line: proven, proven with gaps, or failing, with the counts.
2. **Outcomes**: the plan table with what was seen and the result per promise.
3. **Probes**: how many tried, how many broke, one line per probe that broke, and the areas not probed with the reason.
4. **Found and fixed**: each defect as a person would meet it, the fixing commit, the covering test, and that it fails on the parent commit.
5. **Checks**: checkbox lines with the exact command, the count and the baseline.
6. **Already failing before this change**: each name, shown on the parent commit.
7. **Unproven**: what could not be run here, why, and what would prove it.
8. **For the person**: exact steps in order, each with the condition that proves it worked. When the order matters, say it first: "seed, then deploy".
9. **Decided for you**: each choice made without asking, with its reason.

Filled example (an invented change, for the shape only):

```
## Validation summary

**Result:** proven with gaps: 4 of 5 outcomes proven, 1 of those after a fix, 1 unproven.

### Outcomes
| # | Promise | How it was proven | What was seen | Result |
|---|---|---|---|---|
| O1 | A person can type a name per uploaded file | Preview build, Chromium, test account, two files | Both names in the posted body; screenshots attached | Proven |
| O2 | A blank name is left out of the request | Local backend against the sandbox; direct HTTP and the generated client | No `association_name` key; the stored name is the default | Proven |
| O3 | A name over 80 characters is refused | Direct HTTP at 80 and 81 characters; then the form | First run: 81 accepted and cut to 80. After the fix: 422, and the form keeps the files | Fixed, proven |
| O4 | Callers that send no name keep working | Released client 0.5.3 against the branch build | 200; the default is stored | Proven |
| O5 | Upload from the packaged application | Not run | Needs the signed build | Unproven |

### Probes
Tried 19, broke 1 (O3). Not probed: files over 5 GiB, because no test file of that size exists here.

### Found and fixed
- A name of 81 characters was stored cut to 80 and the caller got 200: fixed in 3fa91c2; covered by `test_attach_file_with_a_name_over_80_characters_returns_422_and_writes_nothing`; fails on the parent commit, passes here.

### Checks
- [x] `poetry run pytest tests/`: 132 passed, 1 skipped (baseline on the parent commit: 126 passed, 1 skipped)
- [x] Playwright, Chromium: 64 passed
- [ ] Playwright, Firefox and WebKit: run at the gate only

### Already failing before this change
None

### Unproven
- O5: needs the signed build; step 1 below proves it.

### For the person
1. Install the staging build, upload one file with a name and one without. Proves it worked: the first shows the typed name and the second shows "default".

### Decided for you
- Tested against the sandbox upstream, not a local stub: the sandbox runs the real validation.
```

Before posting, check every number against the evidence log and remove anything you did not run. Your summary gives the result in one line and links the comment.

### Questions to ask here

- What is still unproven, and who can prove it?
- Which command printed each number?
- Anything in here we did not run? Take it out.
- Are the old failures really old? Shown on the parent commit?
- Could the person act on this comment alone, on a phone?
