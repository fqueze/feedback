# fx-tests feedback (test_submit_without_field_modifications.html)

## The "first failure" in Issues is a todo (TEST-KNOWN-FAIL), not the failure

- Command: `fx-tests test toolkit/components/passwordmgr/test/mochitest/test_submit_without_field_modifications.html`
- Expected: row 5 of "Issues (first failure per run)" to show the failing assertion, which in every job I sampled is
  `Checking form-basic username is: user1 - "" == "user1" - got "", expected "user1" (operator ==)`.
- Got: `69x FAIL The author of the test has indicated that flaky timeouts are expected.  Reason: Giving a chance for the unexpected popup to show`.
  That line is what `SimpleTest.requestFlakyTimeout` emits as a `todo` at finish, i.e. a TEST-KNOWN-FAIL present in
  every run of the test, passing or failing. It is picked as the first failure message, so the Issues table groups all
  69 failures under a message that says nothing about them, and `--issue <n>` cannot split failure modes.
- Workaround: `fx-tests task <id> --messages` on ~10 tasks one by one to learn the real messages.

## Question: which real failure messages are behind a test's failures, with counts

- Command: none answers it; I looped `fx-tests task <taskId> --messages` over 10 task IDs from `--task-ids`.
- What could have shown it: `fx-tests test <path>` listing, per failing run, all its unexpected messages (not just the
  first), or a `--messages` flag on `test` aggregating them over the window.
