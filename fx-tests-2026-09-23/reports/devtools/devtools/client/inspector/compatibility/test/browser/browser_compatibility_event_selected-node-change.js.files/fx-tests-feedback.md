## Question: "which message was this run's unexpected failure?"
- Command: `fx-tests test devtools/client/inspector/compatibility/test/browser/browser_compatibility_event_selected-node-change.js` (and `fx-tests task Xxr5rCPkSwCY8T4P8nONtQ --messages`)
- Expected: "Issues (first failure per run)" shows the first TEST-UNEXPECTED-* message: `waiting for vsync to be disabled - timed out after 50 tries.`
- Got: it shows `handleEvent() was unable to perform a11y checks on hidden node: ...`. The per-test profile logs that message as TEST-KNOWN-FAIL (an a11yWarn todo), so it is not a failure. The resource-usage profile's TestStatus marker also says just `FAIL` with no expected status, which may be where this comes from.
- Workaround: read the test's log in the per-test profile to find the real TEST-UNEXPECTED-FAIL.
- What the output could show: skip expected-FAIL (known-fail) statuses when picking the first failure, or at least tag them.

## Question: "the minidump of this job's crash"
- Command: `fx-tests test devtools/client/inspector/compatibility/test/browser/browser_compatibility_unsupported-browsers_some.js --task-ids --limit 0`, `fx-tests task Xxr5rCPkSwCY8T4P8nONtQ` (also `--json`)
- Expected: the guide says `--task-ids` gives "a task ID and, where the dump was uploaded, a minidump ID". A dump was uploaded (`public/test_info/7b1314b8-f89a-42eb-d98c-6775f5d5372f.dmp`).
- Got: only the task ID, and `task` says CRASH (`@ mozilla::(anonymous namespace)::RunWatchdog`) but gives no minidump ID. `task --json` has no minidump field.
- Workaround: `curl .../task/<id>/runs/0/artifacts` and grep for `.dmp`, then `fx-tests crash <task> <dumpId>`.
