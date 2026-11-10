## Layers

- **When building a service**: entry points (routes, handlers, jobs) are thin: validate, call one operation, map the result or the exception. Business rules and authorization live in one layer every caller passes through. Vendors sit behind stores the system owns. Everything is wired in one module. Dependencies point inward (hexagonal architecture). Hide more behind a smaller interface, and prefer the design that removes a concept to the one that adds a layer.
- **When an invariant must hold for every caller**, enforce it in that layer and prove it with a contract test that walks the route table. Enforcing it in the manager makes the invariant hold for every caller, and the router just forwards.
- **When a layer would learn its children's concerns**, expose the check from the child: a manager of shared files need not know anything about the image formats inside them; it only cares about files.
- **When a component needs a client**, inject it: managers do not instantiate clients themselves.
- **When a vendor SDK type would cross a boundary**, stop it at the adapter, and check by search that no Azure or AWS SDK type appears outside `src/stores/<adapter>/`.
- **When a client is expensive**, build it once at startup (it removes a per-request managed-identity token fetch and TLS handshake from the hot path) and close in reverse order.
- **When wiring is missing**, fail closed and name the cause: the request fails closed (500) rather than being served permissively.
- **When a helper or a primitive already exists**, here or in a sibling repository, reuse it and link it: an existing `Address` model is reused instead of writing another `_ShippingAddress`.

## Contracts

Assume every observable behavior is relied on by some consumer (Hyrum's law): a status code, a default, an ordering, an error sentence.

- **When a service owns writes and has SDK consumers**, choose REST with a committed OpenAPI description, regenerated in pre-commit so CI fails on drift. **When it is a read-only aggregation over many stores**, choose GraphQL with batched loading; a graph layer is rejected for writes, because it is a read-only query layer and mutations would break its contract.
- **When an API changes meaning**, add a version with stated semantics; freeze the old version's behavior and status codes (legacy clients depend on them, so do not fix them), mark it deprecated, and remove it only after its consumers have moved.
- **When a model exists only to keep a client generator happy**, say so in a comment beside it.
- **When a consumer omits a parameter and gets a bad result**, change the server default, not the consumer (raise a default `limit` that truncates lists, from 50 to 200, say).
- **When a request or response is polymorphic**, write one model per variant with a required literal discriminator (an optional discriminator generates broken SDKs), a named union, private base classes and a completeness test. A flat payload fails because nothing stops a card payment from carrying `bank_account_number`.
- **When a field is owned by another system**, expose a string, not an enum. **When an enum is closed**, say so and test its completeness: adding a value is visible to consumers (publish the SDK, upgrade consumers, emit the new value). **When a field is replaced**, deprecate it in the schema; do not delete it.
- **When consumers look a value up by name**, model a map keyed by that name; prefer flatter JSON and fewer wrappers. For example, a map keyed by the association name instead of a list of objects that each carry it.
- **One response type per method**, carrying what the consumer needs, not a boolean.
- **Keep a query grammar small** (`?filter=field__op__value`, `?sort=-created_at`, offset, limit, `has_more`) and validate names as well as values before they touch a query language. An unsupported operation answers 400 instead of silently matching nothing.

## Errors across a boundary

- **One domain exception hierarchy maps to status codes**: 400 invalid request; 401 and 403 access; 404 not found; 409 already exists, conflict, referential integrity; 412 precondition failed; 500 internal, configuration, data integrity. Messages are built in one place and stand alone. The body is `{"detail": ...}`: typed error schemas add little value and clutter the documentation page (a deliberate departure from RFC 9457).
- **When a denial would reveal that something exists**, answer 404 on reads. **When a write would strip the caller's own access**, answer 403 naming keys and never values, so clients can tell a permission problem from a validation problem.
- **When an error is the user's to act on**, pass its status and detail through. **When it is the service's own failure**, answer 502 or 500 with a request identifier and never the upstream detail. A 401 from upstream is deliberately not passed through: it means the service's own on-behalf-of token exchange failed, which is the service's problem, not the user's. The interface shows 4xx detail and never 5xx detail.
- **When a composed read hits one corrupt part**, degrade that part and log it. **When a direct read hits it**, answer 500.
- **When a batch partially fails**, report every failed item with its error: `success = not failed_files`.

## In practice: Python services

Applies when the repository uses FastAPI with Pydantic.

- Layout: `main.py` (lifespan builds shared clients), `settings.py`, `dependencies.py` (one `inject_<thing>` per manager or store), `routers/v1 v2 v3`, `managers/`, `stores/` (abstract bases, one adapter package per backend), `models/` (documents, requests per version, responses), `exceptions/`.
- A router is `try`, one manager call, re-raise a domain exception, log anything else with `exc_info=True` and raise 500. Never pick a status by matching exception text.
- Operation identifiers are `{route_name}_{version}`, so an SDK reads `createProjectV1`.
- Temporal workers: files go through one manager class keyed by workflow identifier; a timeout lives with its activity; one response class per update handler; validate before calling an activity (fail early and fail fast, and save an unnecessary activity call); route heavy work by task queue, not by worker mode.

## In practice: GraphQL

Applies when the repository uses a schema-first GraphQL server as a read-only layer.

- **When adding a data source**, wrap it in a loader class that implements only the query and the primary-key filter, so batching comes for free. **When its shape differs from the schema**, map field paths from the source to the schema on documents and filters; do not change the schema.
- A `.gql` file beside each resolver; snake_case fields that match the sources; filter inputs generated by a directive that fails when the schema is built; loaders created per request.
