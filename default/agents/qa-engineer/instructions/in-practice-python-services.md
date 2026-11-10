Applies when the repository uses Python for a web service: FastAPI or similar, Pydantic and pytest.

- Use `pytest-asyncio` in automatic mode and `pytest-mock`. Tests mirror the source tree.
- Call a route function directly as a coroutine, with a `SimpleNamespace` service whose API objects are `AsyncMock`s; prefer the plain namespace to `MagicMock`. Use the test client only for cross-cutting behavior, with throwaway routes under `/_test/...` on the real application.
- Walk `app.routes` for what must hold everywhere. Add a completeness test wherever a discriminated union mirrors an enumeration.
- Storage adapters: run the error contract as one scenario set against every adapter over mocked client libraries. Creating an existing identifier raises already-exists; getting a missing document returns `None`; replacing or patching a missing document raises not-found; a stale version marker raises a conflict; a failed condition raises precondition-failed. Then check engine-specific operators against the real engine in a container.
- Prove boundaries with a search: no cloud SDK type appears outside each adapter's own folder under `src/stores/`. Prove a lazy import by importing the application with the other adapter's package absent.
- Settings: an invalid combination is refused at startup with a message that explains the constraint; missing wiring fails closed.
- Shutdown: one store's failing close does not stop the others from closing.
- Concurrency: the store's 412 reaches the caller as 409; a synchronous client call inside an async route runs in a thread.
- Models: test both halves of a schema constraint plus validator, with an empty string and with spaces only. A validator that can never run is dead code: show it and remove it.
