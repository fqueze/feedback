# fx-tests feedback

## An expected failure (`todo`) is reported as the failure

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_persistent_storage_permission_indication.js`
  (and `fx-tests task VakMNvkzQhyK1R0wS5XXMg --json`, `messages[0]`)
- Expected: the Issues line and each failing test's first message to be an unexpected failure:
  here `waiting for vsync to be disabled - timed out after 50 tries.`
- Got: `41x FAIL handleEvent() was unable to perform a11y checks on hidden node: id:
  identity-permission-box, …`, which is `SimpleTest.todo(false, …)` from `AccessibilityUtils.js`.
  The job log prints it as `INFO - TEST-FAIL` (status FAIL, expected FAIL), the profile as
  `TEST-KNOWN-FAIL`; by the code it happens in passing runs too (the first subtest clicks a
  `display: none` box). The real failures are in `allMessages` only.
- Cost: the whole headline pointed at an a11y/hidden-node problem that does not exist; it took
  the per-test profile to see both subtests pass.
- Workaround: `fx-tests task <id> --messages`, then skip messages that are expected failures.
- Suggest: drop subtests whose `expected` is FAIL (or whose status equals expected) from
  `messages`, the Issues table and `failures`.
