## A SimpleTest.todo is listed as a failure message

- Command: `fx-tests task cuSk3VDjT4G9eyrk4sX3wg --messages` (and the same for Pq0HFIVqSzy0bQUq9cjjaQ)
- Expected: only the unexpected failures (the two shutdown-leak messages).
- Got: `1x handleEvent() was unable to perform a11y checks on hidden node: …` next to them. That message comes from `a11yWarn`, which is `SimpleTest.todo(false, …)`, logged as TEST-KNOWN-FAIL (status FAIL, expected FAIL). It only appears because the leak made the harness dump the test's buffered log. In the resource-usage profile, its `TestStatus` marker is named `FAIL` and has no `expected` field, so it looks exactly like a real unexpected FAIL.
- Workaround: read `AccessibilityUtils.js` and `browser-test.js` to find out it is a todo.
- What would have answered it: keep `expected` and drop status==expected rows from failure messages, or mark them as known.
