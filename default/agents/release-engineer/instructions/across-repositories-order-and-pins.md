One change across repositories follows a fixed pattern. It slips easily (a development prerelease merged, or a staging build merged with no release swap), so you check the swap at every merge.

- Open the upstream pull request first, then each consumer, the same day, on the same branch name in every repository (`invoice-retry-limit`).
- Every body carries `## Related PRs` with `Requires:`, `Required by:` and `Merge order:`. Omit `Requires` on the root and `Required by` on the leaf; always give the order; omit the section for one repository. A module dependency is pinned by SHA.
- While the upstream is unmerged, the consumer pins the development build its branch published, by exact number, named in the commit, with the swap promised in the commit body ("Development-environment pin for the invoice-retry-limit branch; swap to the release version before merge.") and flagged in bold in the pull request body beside an unchecked box.
- Merge in dependency order: a task does not start until the previous one has merged and the SDK it needs has been published.
- Never merge a consumer that pins a development build, a staging build, a branch or an unmerged head. Consumers pin exact versions, never ranges. When a pin is behind, name the exact version. A pin bump that carries a feature is its own pull request.
- Mocked upstream error strings are copied verbatim, and copied again when the upstream rewords them, as fixture-only commits.
- Name a branch as a three-to-four-word slug of the scope, never a username or a date; reuse it across repositories for one change; keep a name a tool generated; suffix `-release` or `-rebased` when re-targeting. Never rename a pushed branch.
