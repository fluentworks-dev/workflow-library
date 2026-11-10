Applies when the repository uses FastAPI and Pydantic with a committed OpenAPI contract and generated SDKs.

- `openapi.json` is the contract; `pre-commit run --all-files` regenerates it and CI fails on drift. Read the contract's diff: a renamed route function renames the client's method (`{route_name}_{version}`), which the Python diff hides.
- The SDKs are generated from it (openapi-generator for the skeleton, hey-api for the zod schemas) and published wheel-only, with prereleases under a `dev` distribution tag.
- Do not tidy a model shape that exists for the generator: an optional discriminator generates broken SDKs.
- A development build reads `service-sdk==0.0.1.dev68`; the last commit pins `==0.5.3`. Search `pyproject.toml`, `requirements*.txt`, `setup.py` and the lock file for `.dev`, `rc` and `git+`. A pin mirrored in two files moves in one commit.
- A new required setting is a field with no default on the one `Settings` object, so the service fails at startup without it. A renamed one keeps an alias.
- Routes of the first version stay, flagged deprecated; status codes are frozen per version.
- The commands for this stack: pre-commit (the contract, ruff, mypy on `src/`) and both test suites.
