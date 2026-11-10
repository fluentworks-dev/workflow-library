## A change that spans repositories

- **One design, one pull request per repository, upstream first**, the same day, with the same branch name. Each body carries `## Related PRs` with `Requires:`, `Required by:` and `Merge order:`.
- **Consumers pin exact versions. While upstream is unmerged, the consumer pins the development build the upstream branch published**, exactly, with the swap promised in the commit body (swap to the release version before merge) and flagged in bold with an unchecked item. The last commit before merge pins the release; bump again when a release tag predates the merge it should contain.
- **The common mistake** is a development build merged to the default branch. The design lists the swap as a step of its own.
- **Mocked upstream error strings are copied verbatim**, and copied again when the upstream rewords them.

## Numbers, performance and cost

- **Freshness, limits and retention**: for example, "running" means a status within the last 5 minutes; a 7-day partition window has a full-history fallback so a device that has not reported recently still resolves; a limit that refuses tells the user what to do next.
- **A sandbox is the smallest and the shortest**, with its monthly price in the comment. An environment that keeps data hardens those inputs and never loosens them to make a destroy pass.
- **Measure performance before and after in the closest environment you may use, never on production data.** The production measurement is an exact step for the person.
- **The recurring moves**: collapse N+1 reads into one query through a batching loader; a predicate on the partition column with a fallback for the keys it misses; sorted key lists so a rendered query is a stable cache key; a cache with an explicit size and time to live.
- **Filter, sort and page a list in the database, not in the service.** The database is almost always the answer, because it scales much further, unless it cannot do the job or there is a good reason not to. Reading every row to filter it in the service holds at today's size and fails at the next. Shape the contract to what the database does exactly: sort only by fields it orders the same way on every page, and give each filter an index. Do it in the service only when the database cannot, or for a reason you write down with the number of rows it holds up to and what the caller gets past that.
- **Right-size cost continuously, in small pull requests**, not in one optimization pass.

## Operation

- **Operator preconditions and post-merge steps are in the design and the pull request**, in order: seed, then deploy.
- **Be honest about what can be observed**: when the service has no metrics yet, say so, and name the first counter to add when metrics arrive (reads of corrupt rows, for example).
