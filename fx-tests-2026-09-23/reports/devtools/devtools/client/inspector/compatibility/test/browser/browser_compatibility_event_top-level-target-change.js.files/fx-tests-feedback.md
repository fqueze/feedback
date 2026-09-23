## `fx-tests test` and `fx-tests task` report a TEST-KNOWN-FAIL (todo) as the failure

- Command: `fx-tests test devtools/client/inspector/compatibility/test/browser/browser_compatibility_event_top-level-target-change.js` (also `fx-tests task Xxr5rCPkSwCY8T4P8nONtQ --profiles`)
- Expected: the failure under "Issues (first failure per run)" and on the task row to be the TEST-UNEXPECTED-FAIL, `waiting for vsync to be disabled - timed out after 50 tries. - false == true`.
- Got: `FAIL handleEvent() was unable to perform a11y checks on hidden node: ...`, which is `SimpleTest.todo(false, ...)` from AccessibilityUtils' `a11yWarn`, logged as TEST-KNOWN-FAIL (the per-test profile's parent main thread has it as `TEST-KNOWN-FAIL`). It is chronologically first, but it is not a failure. Same for browser_compatibility_event_selected-node-change.js and browser_compatibility_settings.js in that job, where it made a vsync failure look like an a11y-checks failure.
- Workaround: `fx-tests task <id> --messages`, then the per-test profile's `Test` markers to see the status of each message.
