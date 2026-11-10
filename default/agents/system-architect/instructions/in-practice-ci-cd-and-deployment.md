Applies when the change alters how a service is built, versioned, deployed or promoted.

- When adding a deploy environment, add a thin trigger that calls the one reusable job with `secrets: inherit`, bind the environment so its protection gates production, and give it a unique version prefix.
- Lanes: development deploys by hand, staging on a push to the default branch, production on a published release whose tag is the version.
- Encode the environment in the version (`0.0.0-dev.<run_number>` for staging, `0.0.1-dev.<run_number>` for development), because the run number is shared by every workflow in a repository and parallel runs must not collide; each image also carries the commit hash.
- Federate with OIDC. Production deploys to a slot, and a person swaps it.
