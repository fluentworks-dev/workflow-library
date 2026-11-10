A prerequisite is anything that must be true before this merge is safe. Find them in four places: the rollout section of system-design.md; the pull request body (a Deployment note, Before merging, a warning alert, Post-merge steps); `Related PRs`; and the diff, which is the one that does not forget. Read the diff for new settings, migrations, indexes, infrastructure, removed or tightened contracts, and pins.

Sort each one:

| Kind | What you do |
|---|---|
| A fact you can check: a release exists, a variable is set, a plan reports no changes, nothing calls the removed route | Check it, and record the command and what it showed |
| An action only the person can take before the merge: an apply from the branch, a release of the provider, a secret set in an environment, a migration run by hand | This is the workflow's one batch before the merge: ask once with AskUserQuestion, all such actions together with every point earlier stages left for the person, by the shared rule on when to ask. Each is an exact step with the condition that proves it worked. After the answer, check the condition yourself |
| An action that can only happen after the merge | Not a prerequisite. It goes into "After merge: a person must" |

How to check the common ones:

- A new required variable: find its name in all four places and in the documentation table, then list each environment's variables and confirm it is there. Confirm a secret by name only; never print it. The rules behind it: never put values that differ by environment at repository scope, or the reverse; manage the runtime environment variables in one place, not some in the code and some in Terraform.
- A removal: list what the diff removes or tightens (routes, operation identifiers, fields, parameters, defaults, status codes, setting names, columns, file names another service reads); search each sibling repository's default branch for the name and for the generated client's method name; report the repositories, the names and the callers found.
- A pin: the release exists in the registry and its tag contains the provider's merge commit.
- A migration: read how this repository applies migrations, then the migration file itself; additive and applied before the code serves, or it does not merge.
- Infrastructure: the plan at the current head reports "No changes" and the body says what was applied by hand.

A step for the person has four parts: the place, the command, what to expect, and the condition that proves it worked.

```
Before merging, apply the bootstrap role from the branch:
1. In `infra/bootstrap/deploy-role`, signed in as the bootstrap service principal: `terragrunt plan`
   Expect: 4 to add, 0 to change, 0 to destroy.
2. `terragrunt apply`
   Expect: "Resources: 4 added, 0 changed, 0 destroyed."
Worked when: the plan job on this pull request reports "No changes" for every unit.
```

In practice: applies when the repository deploys with GitHub Actions. Each lane is a thin trigger of this shape; a lane that does more, or lacks `secrets: inherit`, is a finding: Without the last line, "GitHub resolved them to empty strings even though the job binds environment: <env> and the vars from the same scope resolved correctly". Lanes carry different version prefixes because "run_number is shared across workflows in the same repo".

```yaml
on:
  release:
    types: [published]
jobs:
  deploy:
    uses: ./.github/workflows/deploy.yml
    with:
      environment: production
      version: ${{ github.event.release.tag_name }}
    secrets: inherit
```

In practice: applies when the repository deploys with a Bash script. A new required variable is in both the list and the allowlist:

```bash
set -euo pipefail
environment="${1:?Usage: deploy.sh <environment> <image-tag>}"
export IMAGE_TAG="${2:?Usage: deploy.sh <environment> <image-tag>}"

REQUIRED_VARS=(REGISTRY APP_NAME ARCHIVE_STORAGE_ACCOUNT_URL)
for name in "${REQUIRED_VARS[@]}"; do
  [[ -n "${!name:-}" ]] || { echo "Missing required environment variables: $name" >&2; exit 1; }
done

rendered_template="$(mktemp)"
trap 'rm -f "$rendered_template"' EXIT
# Only the listed names are substituted; any other $ in the template is left alone.
envsubst '${REGISTRY} ${APP_NAME} ${ARCHIVE_STORAGE_ACCOUNT_URL} ${IMAGE_TAG}' \
  < deploy/template.yaml > "$rendered_template"

slot_flags=()
if [[ -n "${SLOT:-}" ]]; then slot_flags=(--slot "$SLOT"); fi
echo "environment=$environment image_tag=$IMAGE_TAG application=$APP_NAME slot=${SLOT:-none}"
cloud_deploy --file "$rendered_template" "${slot_flags[@]}"
```

In practice: applies when the repository uses Terraform and Terragrunt. The body of an infrastructure change, at the merge:

```
## Before merging
- [x] Bootstrap role applied from the branch as the service principal: 4 added, 0 changed, 0 destroyed.

## Already applied
- `infra/bootstrap/deploy-role` (by hand, 2026-11-03). CI must report no changes for it.

## Test plan
- [x] `terraform test` in all 10 modules: 64/64 pass
- [x] `terragrunt hcl validate --inputs --strict`: passed
- [x] Plan at the current head: No changes in 14 units; 3 to add, 0 to change, 0 to destroy in `event-stream`
```

Do not merge, and reorder or split, when:

- a migration or a setting would arrive after the code that needs it;
- a removal would land before its last reader is gone: split the removal into a later pull request;
- a consumer still pins a development build, a branch or an unmerged head;
- an infrastructure plan at the current head reports changes, or shows a destroy or a replacement the person has not read;
- a new required variable is missing from any of its four places, or from any environment;
- the rollout needs a release the person has not confirmed.

How to word these. An operator precondition: "seed, then deploy"; "Apply order: the database before the application"; "time the apply against the consumer's cutover rather than the merge". A rollback that is not symmetric: "a rollback after cutover is silently lossy." A removal with no caller, and how that is known: "Not reinstating the param ... No caller passes it"; "Wire-level compatibility is preserved (FastAPI ignores unknown query params; a router test pins this)." A pin left behind, which a reviewer otherwise has to ask about: was this intended?

When the rollout section is weak, rewrite it:

| Weak | As it should read |
|---|---|
| "Run the migration and deploy." | "1. Apply `0019_add_archived_at` (one nullable column; the deploy applies it before the switch). 2. Deploy. 3. Run `scripts/backfill_archived_at.py --dry-run`, read the count, rerun without the flag. Rollback: safe before step 3; after it, a rollback keeps the column and loses nothing." |

When nothing is needed, say so in one line and why: "Prerequisites: none. The change adds no setting, migration, infrastructure or dependency, and removes nothing."

Write the list as the second section of the merge notes. Each row holds the condition, how it was checked, and a state: Verified, Done by the person and verified, After the merge, or Blocked. Close with the order and with what a rollback loses when it is not the mirror of the merge.

```
### Prerequisites
| # | Must be true before the merge | How it was checked | State |
|---|---|---|---|
| 1 | The storage account exists in every environment | applied from the branch by the person; the plan at head 5e4d3c2 reports "No changes" in 14 units | Done by the person and verified |
| 2 | `ARCHIVE_STORAGE_ACCOUNT_URL` is in all four places and the documentation table | all five are in the diff; `gh variable list --env` showed it in development and staging, not in production; the person set it; listed again, present in all three | Done by the person and verified |
| 3 | Migration `0019_add_archived_at` applies before the new code serves | one nullable column; this repository migrates in the deploy, before the switch | Verified |
| 4 | Nothing still calls the removed `attach_file_v1` | searched the default branches of the two consumer repositories for the route and the client method: 0 callers; deprecated for two releases | Verified |
| 5 | The index on `archived_at` | declared as data; applied by `scripts/apply_indexes.py` at deploy time | After the merge: listed for the person |

Order: 1 and 2 before the merge; 3 by the deploy; 5 after the staging deploy.
Rollback: not symmetric. Files archived after the deploy live only in the second account; a revert leaves them unreachable until the change is rolled forward again.
```

**Questions to ask here**

- Does any new environment variable need adding to the Terraform configuration?
- Is it set in every environment, or only the one we tested?
- Does the migration run before the code that reads it? How does this repository apply migrations?
- Is the release we pin published? Does it contain the upstream merge?
- Was this applied from the branch? Does the plan say "No changes" now?
- Does this work in every cloud partition and region it deploys to?
- Did a reviewer set a condition, such as getting the latest version before merging?
- Is this the smallest instance type that does the job? A sandbox needs only the smallest.
- What does a rollback lose?
