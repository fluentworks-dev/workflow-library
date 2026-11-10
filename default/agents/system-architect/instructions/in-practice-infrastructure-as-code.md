Applies when the change adds or alters cloud resources, identities or deployment roles that are declared in code and applied to a live account.

- When building a tree of units, give each level its own identity file and have every unit write out every module input: nothing arrives by invisible inheritance, so reading one unit file tells you the whole story.
- When a value could change independently per component, keep it a local; add a variable only when environments differ; give an escape-hatch input an empty default.
- When naming resources, put the type first, validate the slugs, compose every name in one map, and pin every name in an offline test.
- When pinning, pin tools and providers exactly, in one place each, bump them in one-line pull requests, and do not commit lock files: a deliberate departure from the vendor's advice, to prevent provider version conflicts across machines.
- When a module may run in a government partition, compose resource names from the partition, keep region literals out of modules, and check service availability first.
- When trust roots, resources that cannot be tagged, or first applies that need special credentials are involved, a person applies from the branch before the merge and CI must then find no changes; destroys are never automated.
- When a plan wants to replace an immutable resource for a convention cleanup, add an override that keeps the old name.
- When code and cloud may have drifted, plan across every environment first and align the code to what is live before adding the change, naming each drift.
- When writing a runbook, give exact commands, expected plan counts, the acceptance condition and a table from symptoms to causes; write the recovery the day the failure is first seen.
- When a unit cannot exist yet, add it excluded, with a comment naming the enabling step, so validation still checks its inputs.
