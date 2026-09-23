
## Question: which test logged each of these TEST-* markers? (review, 2026-09-22)

- Command: `profiler-cli thread markers --session <s> --category Test --search "not focusable" --list --limit 0`
- Expected: a way to see each row's `Test Name` field, since the same assertion message is logged by many tests of the manifest in one per-test profile (here 29 rows across 16 tests).
- Got: rows show only name, time and message; the test name needed `marker info m-28..m-55 --json` piped through a script.
- Could have shown: a `--fields test` option (or a test-name column for `TestStatus` markers) on `--list`.
