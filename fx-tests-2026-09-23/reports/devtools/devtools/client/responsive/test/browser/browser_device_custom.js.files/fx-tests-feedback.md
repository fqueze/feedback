# fx-tests feedback (browser_device_custom.js)

## `try` reports TEST-KNOWN-FAIL (todo) results as the failure message

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --messages --task-ids --full-messages --limit 0`
  (also `fx-tests task ct9m-aJqRqGxz_JUAX_2mQ --profiles`)
- Expected: the row's first message to be the TEST-UNEXPECTED-FAIL that made the test fail:
  `Interactive elements must be labeled: id: device-close-button, ...`.
- Got: the first (and only, without `--messages`) message shown is
  `handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: button, className: command`,
  which is an `a11yWarn` -> `SimpleTest.todo(false, ...)`, i.e. TEST-KNOWN-FAIL in the test's
  log. With `--messages`, five of the six "failure" messages listed are such todos, each
  counted "6x" like a failure; the real failure is last.
- Workaround: loaded the per-test profile and read the `TEST-UNEXPECTED-FAIL` markers.
- Could have shown: only unexpected results as failure messages, or known-fail ones tagged as
  such (the resource-usage profile's `FAIL` marker name for these todos is probably the source).
