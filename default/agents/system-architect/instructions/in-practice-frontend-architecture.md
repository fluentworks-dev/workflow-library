Applies when the change touches a web application or the backend that exists only to serve it.

- When a mutation exists, give it its own route and action, keep detail pages pure readers, and export the form's schema beside the form for the action to reuse.
- When a pure rule or vocabulary is needed, put it in a small module free of the framework and of dependencies, with the reason in its header, a unit specification and a comment naming its source of truth.
- When showing an error, name the class of failure, show the service's sentence for a client error, never show detail for a server error, and keep all wording in one file. A missing item and a denied one read the same.
- When a value is unknown to the frontend, render it raw instead of hiding it.
- When a product rule is undecided, ship the read path and gate the write path, with the sentence in the pull request.
- When adding a control, give it an accessible name and select it by role or label in tests.
- Validate on submit, keep a server error until the next real submit, and never let an untouched control mark the form as changed.
