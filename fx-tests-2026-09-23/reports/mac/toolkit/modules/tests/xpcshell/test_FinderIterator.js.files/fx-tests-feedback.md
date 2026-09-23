## Question: which failure mode is each failing job, and which configs does one failure mode hit?

- Command: `COLUMNS=250 fx-tests test toolkit/modules/tests/xpcshell/test_FinderIterator.js --task-ids --limit 0`
- Expected: one list of the failing jobs, each tagged with its issue number (or grouped under its issue).
- Got: the jobs grouped by issue without the group headers, and the issue-1 group printed twice (the 10 macOS/Windows jobs appear again after the list), so 21 jobs read as 31 rows. Only `--issue <n>` per issue made the grouping readable.
- Workaround: ran `--issue 1`, `--issue 2`, `--issue 3` separately.

## Question: is a TIMEOUT of this test the test's own, or the whole job breaking?

- Command: `fx-tests test <path>` Issues list, `2. 8x TIMEOUT Test exceeded time limit`.
- Got: presented like a failure mode of the test. Each of the 8 jobs (`fx-tests task <id>`) had 267-1563 TIMEOUTs out of 548-2056 tests: the whole msix job broke.
- What would have answered it: flag an issue whose jobs had a large share of their tests fail the same way ("job-wide in 8 of 8 jobs").

