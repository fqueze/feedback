## Question: what happened to one test in one job?

- Question: "in task LKys8j8i, what was test_objectgrips-18.js's outcome and message, and is there a per-test profile?"
- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g --profiles` truncates to the first ~20 of 488 failures, so the test is not shown; needed `--limit 0 --full-messages` then `rg -A3 test_objectgrips-18`.
- What could have shown it: `fx-tests task <taskId> --test <path>` restricting the rows to one test.

## `fx-tests test <path> --bugs` prints nothing when no bug names the test

- Command: `fx-tests test devtools/server/tests/xpcshell/test_objectgrips-18.js --bugs`
- Expected: a line such as "No bug names this test".
- Got: the same output as without `--bugs`, with no Bugs section, so it is unclear whether the search ran.
