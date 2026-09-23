## Expected failures (TEST-KNOWN-FAIL / todo) shown as the test's failure

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0` (also `fx-tests task ct9m-aJqRqGxz_JUAX_2mQ --profiles`)
- Expected: the headline message for `browser_device_custom_remove.js` to be its `TEST-UNEXPECTED-FAIL`, `Interactive elements must be labeled: id: device-close-button ...`.
- Got: the headline is `handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button, className: command`, which the profile shows is a `TEST-KNOWN-FAIL` (a `todo` from `AccessibilityUtils.a11yWarn`, Expected: FAIL). With `--messages`, three of the five listed "failure" messages are todos, and the real failure is fourth.
- Workaround: read the Status/Expected of each message's `TestStatus` marker in the per-test profile.
- What would help: leave out, or mark, messages whose status equals their expected status.
