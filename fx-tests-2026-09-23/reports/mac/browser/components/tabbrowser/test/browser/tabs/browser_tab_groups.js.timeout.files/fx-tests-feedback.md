## Question: "in which subtest, and after which step, did each of these 20 timeouts hang?"

- Command: `fx-tests task <taskId> --profiles --messages`, for each of the 20 TIMEOUT tasks of `fx-tests test <path> --task-ids --limit 0 --issue 2`.
- Expected: something that tells the timeouts apart, such as the last `Entering test ...` / last `INFO` or `TEST-PASS` line before `Test timed out`.
- Got: the same three lines for all 20 (the two a11y TEST-KNOWN-FAIL messages and `Test timed out`), which cannot say whether they are one failure or several. The only way to tell was to load each ~100–125 MB per-test profile in profiler-cli (1–3 minutes each) just to read the last two log lines.
- What would have answered it: for a TIMEOUT, print the name of the subtest that was running (the last `Entering test`) and the last message before the timeout. Grouping the timeouts under `fx-tests test --issue` by that subtest would have answered it in one command.

## `task --messages` hides "Passed when the harness reran it" when the job had several failures

- Command: `fx-tests task b760OHQpQJm3eMhkhVjiDw --profiles --messages`
- Expected: the per-test "Passed when the harness reran it." line, as for single-failure jobs.
- Got: the line is replaced by a job-level "All 5 passed when the harness reran them." above the list. Extracting one test's block (sed from the test path to the blank line) then reads as "retry did not pass". Minor; cost one extra command per such task to check.
