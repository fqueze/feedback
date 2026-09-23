## `fx-tests task` has no way to ask about one test

- Question: "what did this job record for test_breakpoint-21.js" in each of 6 failing jobs.
- Command: `fx-tests task <taskId> --profiles | grep -A3 test_breakpoint-21.js`
- Expected: the test's row.
- Got: nothing; the FAILED list is truncated (1061 failing tests) and the row was past the cut.
- Workaround: `--limit 0 --full-messages --messages` and grep. A `--test <path>` filter (or printing the asked-for test's row first) would answer directly.

## `fx-tests test --durations` hides the failing config

- Command: `fx-tests test devtools/server/tests/xpcshell/test_breakpoint-21.js --durations`
- Expected: the duration row of the only failing config (test-windows11-64-25h2/opt-xpcshell-msix), to compare with the 45 s TIMEOUTs.
- Got: the 10 slowest configs; the failing one was in the "29 more".
- Workaround: `--limit 0 | grep`. Always listing the failing configs' rows would answer it.
