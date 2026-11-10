Applies when the repository uses Terraform modules with Terragrunt units.

- The commands for this stack: `terraform fmt -check`; `terragrunt hcl format --check`; `terraform test` in every module; `terragrunt hcl validate --inputs --strict`, which checks inputs that `terraform validate` never checks; and a plan, with its counts in the test plan.
- Body sections: Before merging, Already applied, Reviewer notes, and `Related PRs` with SHA pins.
- A "must be replaced" for a naming change gets an override input. Names live in one `local.names` map pinned by offline tests.
- The state key is a literal on purpose: wiring it to a slug means editing the slug silently relocates every unit's state. A plan in which an existing unit is all "to add" means the state moved: stop.
- Providers and tools are pinned exactly, in one place each. Lock files are not committed. A provider held below a version not yet available in a region the repository deploys to is not bumped at the merge.
- A consumer's SHA pin points at the module repository's default branch.
- No `arn:aws:` or region literal in a module, so the module works in every cloud partition.
