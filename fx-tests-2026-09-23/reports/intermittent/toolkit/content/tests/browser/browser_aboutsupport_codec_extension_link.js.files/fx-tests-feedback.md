## Question: "which jobs had a harness retry of this test that passed?"

- Command: `fx-tests test toolkit/content/tests/browser/browser_aboutsupport_codec_extension_link.js --harness mochitest --task-ids --limit 0 --since 2`, then `fx-tests task <id>` on each.
- Expected: a way to list jobs where the first run failed and the retry passed, so a passing run can be read in the same resource-usage profile (the test has no per-test profile: the failure is a shutdown leak).
- Got: the `×2` mark in the `--task-ids` list did not track failing executions: `RU435tceSZ6_1XOySaPxvw` and `OiTe-JxwSOi6v_nOJeRb9w` had no `×2`, yet `fx-tests task` reported "2 failing executions of 2". Some task IDs also appear twice under one day with no mark. `--executions` gives only totals (187 jobs, 135 with more than one failure) and no task IDs.
- Workaround: run `fx-tests task <id>` on candidates one by one until one said "1 failing execution of 2 / Passed when the harness reran it" (`ZRDpUsvRSpaB7-bRkSdwKQ`). A per-task "retry passed" flag in `--task-ids` would answer this.
