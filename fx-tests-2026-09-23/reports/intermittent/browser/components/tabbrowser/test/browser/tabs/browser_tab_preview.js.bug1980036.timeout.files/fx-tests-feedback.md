## Question: which subtest timed out, per failing job (browser_tab_preview.js, issue 3 "TIMEOUT Test exceeded time limit")

- Command: `fx-tests test browser/components/tabbrowser/test/browser/tabs/browser_tab_preview.js --task-ids --limit 0 --issue 3`
- Expected: the subtest each timeout happened in (the log line is `TEST-UNEXPECTED-FAIL | <path> | <subtest> - Test timed out`), or at least a way to group the issue by it.
- Got: task IDs only; the issue text is the harness-level `Test exceeded time limit`, which is identical for every subtest. This test has 4 distinct timing-out subtests with different causes (140 / 28 / 15 / 5 jobs).
- Workaround: downloaded all 181 `live_backing.log`s with curl and grepped `| <subtest> - Test timed out`.
- Could have shown: the subtest name next to each task ID, or split the TIMEOUT issue by subtest.

## Question: per-config counts of one failure mode

- Command: `fx-tests test <path> --issue 3`
- Expected: the "Failing configurations" table restricted to the selected issue.
- Got: the table stays the all-failures table; per-config counts for the issue needed a script over the `--task-ids` list (counting `×2` as two).
