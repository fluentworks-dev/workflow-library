Applies when the repository uses Terraform modules or a Terragrunt tree.

- Offline first: `terraform fmt -check`, `terragrunt hcl format --check`, and `terragrunt hcl validate --inputs --strict`, which checks inputs that `terraform validate` never checks.
- `terraform test` with a mocked provider and `command = apply`, so the provider's own name validation runs as well, and `expect_failures` for every validation rule. Report a count per module.
- Every resource name is pinned in an offline test: a changed name is a failing test first and a decision second. A unit that cannot exist yet stays excluded, and its inputs are still validated.
- A rename for tidiness fails the plan like an unexpected destroy: renaming an applied resource is not a free convention cleanup.
- Plan the environments when the repository gives this session a read-only role; without that role, the plan is the first step you write for the person.
- Search the plan output for secret values.
- Test plan items: no `arn:aws:` literal and no region literal in a module; sandbox sizes the smallest, with the price in the comment; tools at the versions of the one pinned file.
