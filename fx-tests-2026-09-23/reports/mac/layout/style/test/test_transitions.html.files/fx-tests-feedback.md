## `fx-tests test` names a `todo()` as the failure

- Command: `fx-tests test layout/style/test/test_transitions.html` (also `--task-ids`, `--bugs`)
- Expected: under Issues, the `TEST-UNEXPECTED-FAIL` message of each failing run: `uncaught exception - TypeError: can't access property 1, String(...).match(...) is null`.
- Got: `29x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: untriaged`. That line is a `SimpleTest.todo()` (TEST-KNOWN-FAIL) that every run of a test calling `requestFlakyTimeout` emits, pass or fail; it is not a failure. The real message only appears with `fx-tests task <id> --messages`, as the second of two.
- Workaround: `fx-tests task <taskId> --profiles --messages` on a few failing tasks.
- Question it should answer: "what is this test's failure message", from `fx-tests test`. Known-fail/todo lines should not count as failure messages (or should rank after an UNEXPECTED one).
