Applies when the repository uses GitHub Actions to build and deploy.

- Cloud access is OIDC federation with `contents: read` and `id-token: write`, and no stored cloud secret. Attestations stay disabled where the comment says why (for example, a registry reached over a private network that does not support the artifacts).
- Commands: `gh pr checks <number> --watch` by itself; `gh variable list --env <name>`; `gh pr merge <number> --squash`; `gh run watch <run-id> --exit-status`. `gh release create` is the person's.
