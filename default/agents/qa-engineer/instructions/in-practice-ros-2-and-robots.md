Applies when the repository uses ROS 2 nodes, or other code that controls a robot or a device this session cannot run.

- When testing ROS code, test without the runtime, so the tests run on any machine: stub `rclpy` and the interface packages in `sys.modules` before import, or load the module by file path; construct a node with `__new__`, or call the unbound method on a `SimpleNamespace` self; patch sleeps and clocks; skip at module level, with the reason, when the real interfaces are required. Keep the stubs in one module.
- The node must not die: it must not raise, and failures are caught and logged so the node stays alive. The one deliberate exception: where a missing cloud record would silently detach a run from its launch, an upload failure must fail configure. Test both.
- Queue: fill it. The first drop on a machine that has just started is logged too; a "last logged" time of 0.0 hides it.
- Retries: bounded, with backoff, each one logged, one client per batch, failures listed per file, success false when any file failed. Where the code retries every exception by the person's stated preference, apply it, and record in the validation summary what it costs on a permanent failure.
- Rotation: file names are index-first and zero-padded.
- The writer raises `ValueError`; the node validates, warns through the ROS logger and falls back to the default. Assert both.
- Lifecycle and threading are a known weak point, so check them against the platform's guideline: parameters declared in `__init__`, read and subscribed in `on_configure`, consumers started in `on_activate`, flushed in `on_deactivate`, destroyed in `on_cleanup`, `on_error` running deactivate then cleanup; timers over threads; the queue drained on deactivate. Move a subscription only with measured evidence, and defer quickly to the domain expert.
- Options an operator can set are kept in this domain: test each one the change could affect.
- The test on a real robot follows the repository's test plan template. When you do not know how the environment is set up, find out, and ask only when it cannot be found out, in the one batch after testing.
