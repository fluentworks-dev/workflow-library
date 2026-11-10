Skip this stage and the next when system-design.md is a true three-line document. When the ticket has no system-design.md (its board has no System Design column), apply this stage and the next to the system decisions code-design.md makes, and say so in design-review.md. Judge the foundation before the wiring: stored data and contracts first, components after.

**Stored data.**

- Each fact has one home. Nothing is stored that the structure already determines or that can be computed.
- Invalid states cannot be written down: a required discriminator per variant, closed sets that fail on an unknown value, fields that are always present declared non-nullable. Reject a flat payload where nothing stops a card payment from carrying `bank_routing_number`.
- The shape is flat: a map keyed by the name clients look things up by instead of a list to scan, and no wrapper around one field.
- Stored, request and response shapes are separate types. A value owned by another system passes through as a string.
- A store that serves old versions can still read them after this change: there, historical readability beats strictness at write time.
- A name that enters a shared namespace is qualified, and every name that will be persisted is spelled correctly now.
- Timestamps compared as strings use one format with an explicit offset.

**Contracts.**

- Each contract is shown by examples of use first. Each version has stated semantics: a new meaning is a new version, and the old one keeps its status codes and its flaws until its consumers have moved.
- Changes are additive. Anything that can make an existing reader fail is called breaking, including any change of nullability, and has a deprecation and an order.
- Every consumer is listed, found by searching the sibling repositories, not from memory.
- When a consumer gets a bad result by omitting a parameter, the server's default changes, not the consumer.
- Error behavior is designed: which failures the caller can act on and sees in full, and which are the system's own and carry only an identifier. A denied read does not reveal existence.
- Old rows are read by a fallback, not rewritten by a job. A rewrite that cannot be avoided is expand, migrate, contract, each step repeatable, with a dry run, safe beside the live system. A renamed setting keeps its old name working.

**Structure.**

- Each component has one job. Entry points are thin. A rule that must hold for every caller sits in the layer every caller passes through, and a parent or shared component does not know its children's concerns.
- Vendors and remote systems sit behind an interface the system owns, with its error behavior written down. Wiring is in one place.
- A new component, process or store is weighed against the design that adds none: the same worker on a different queue may already solve the problem.
- What the organization already has is reused: its clients, libraries and primitives, and a sibling repository's pattern, ported and named as a port.
- Artifacts are keyed by the platform's identifiers and files are organized by their owner's identifier, so they can be found and cleaned up.

**Configuration.**

- A value that selects an environment is required. A value that tunes is optional with a documented default. Everything is validated at startup, and a backend or mode is a closed set, so a typo fails at load.
- The same names in every environment. A new variable is added in every place it must appear, in the same change. Build-time and run-time values are labeled.
- Defaults live with what they configure, once. A flag and an input that mean different things are both kept, with the difference written down.

**When the design touches a data pipeline, also check** that the pipeline tests run in CI and not only by hand (validating a bundle is not a test run); that the production target overrides any development default of a secret with a placeholder; that alerts go to a team address held in configuration; that windows, lags and lookbacks carry numbers; that a backfill is verified with counts (0 missing, 0 extra, and 0 value mismatches) and says what a rollback loses; and that a table a consumer depends on has a freshness and row-count check.

**When the design touches a web application, also check** for a second writer to any piece of state; that server data is not copied into client state; that a cache is bound to the request, so parallel reads share one result; and that tests intercept every backend call and assert the exact body sent.

**When the design touches a published contract or a generated client, also check** that the change is exercised through every generated client and not only over HTTP; that a shape forced by a generator is recorded beside the model; that the repository's version rule was read and is quoted; and that mocked upstream errors are verbatim copies.

**Questions to ask here**

- Can the stored `id` go?
- Can this list be a map keyed by the name clients look things up by?
- Can these two fields be non-nullable?
- Does this extra namespace add anything?
- Is this name global to all data in the organization? Then it needs a qualifier.
- Does this shared component know about a child's concern when it only needs to handle files?
- Who else reads this, and did we search for them or assume?
- What does an old client see after this ships?
- Is this a new version, or did the meaning of the old one just change?
