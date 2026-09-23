## Question: at which subtest / step did each failing execution hang?
- Command: `fx-tests test toolkit/components/ml/tests/browser/browser_ml_native.js --since 10 --task-ids` and `fx-tests task <id> --messages`
- Expected: the subtest name that the harness puts in the message (`test_ml_smoke_test_llama_crash - Test timed out`), which is what tells this test's 22 timeouts apart (4 different subtests, 3 mechanisms). Sheriff annotations (`fx-tests intermittent --bug`) do show it.
- Got: `Test exceeded time limit` in `test`'s Issues, and `Test timed out` in `task --messages`, with no subtest name. For T6uE, `task --messages` printed `Test timed out` with `@ std::__throw_out_of_range` under it, which mixes a crash signature found at shutdown into the timeout message.
- Workaround: downloaded 17 live_backing.logs and grepped the lines before each `Test timed out`. Adding the subtest name, and the last `INFO` line before the timeout, would have answered it.

## `--since` takes days, not a date
- Command: `fx-tests test <path> --since 2026-09-12`
- Expected: a date, since `--history` prints dates and the step change is read as a date.
- Got: `--since expects a non-negative integer`. Workaround: `--since 10`.
