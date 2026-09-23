## Question: the task IDs of the try jobs where one test passed

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/application/test/browser/browser_application_panel_telemetry-start-worker.js --task-ids` (and `--json`)
- Expected: with `--task-ids`, the task IDs behind each config row (passed / passed on retry / failed), so a passing run's resource-usage profile can be loaded to compare with a failing one.
- Got: only per-config counts (`jobs passed passed-on-retry failed`); `--task-ids` is silently ignored, and `--json` has no task IDs either.
- Workaround: grepped the full failure listing for tasks of the same chunk that failed on other tests.

## Question: which bug are this test's failures starred on

- Command: `fx-tests test devtools/client/application/test/browser/browser_application_panel_telemetry-start-worker.js --bugs`
- Expected: the bugs sheriffs star this test's 67 crashes on. They are starred on bug 2036221 ("Intermittent Assertion failure: !mActor, at .../RemoteWorkerController.cpp:X", 23 annotations in 7 days, all macosx1500-aarch64 debug devtools-chrome), and bug 1904058 is the older one for the same assertion.
- Got: no bug section at all; the output is identical to plain `fx-tests test`. Neither bug names the test in its summary, so a summary-based search cannot find them.
- Workaround: Bugzilla REST search on `short_desc` substring `RemoteWorkerController.cpp`, then `fx-tests intermittent --bug 2036221`.
- What would have answered it: match on the failure message or crash signature (`Assertion failure: !mActor`), or list the bugs sheriffs annotated this test's failing jobs with.
