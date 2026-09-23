## Question: "does this test run close to its timeout when it runs in parallel?"

- Command: `fx-tests test toolkit/components/extensions/test/xpcshell/test_ext_webRequest_mergecsp.js --durations --limit 0`
- Expected: pass durations split by execution mode (parallel vs sequential/retry), and each config's effective timeout next to them.
- Got: one distribution mixing both. On the configs where every parallel run times out (test-macosx1500-aarch64-vms/debug-xpcshell, test-linux2404-64-artifact/debug-xpcshell), every pass is a sequential retry, so the median (9.6 s, 16 s) looks far from the 60 s budget, while the parallel runs never finish. Passes above the budget (70 s at a 60 s budget, 129 s at 120 s) are also unexplained without the timeout column.
- Workaround: read `--executions` (100% of failures parallel) and the job log's `Using harness timeout of 30s (base=30s, factor=1.0)` plus the manifest's `requesttimeoutfactor` by hand.

## `--bugs` text output says nothing when there is no bug

- Command: `fx-tests test <path> --bugs`
- Expected: a line such as "Annotated bugs: none".
- Got: the same output as without `--bugs`; only `--json` showed `"annotatedBugs": []`.
- Workaround: `--json`.

## `task` misses the passing retry, and counts a SKIP as an execution

- Command: `fx-tests task IYyCUf1YSa6jUovnDSBhMA --profiles --limit 0` (and `TAOJNTmcQCiDti01vVH5MA`)
- Expected: "Passed when the harness reran it." for test_ext_webRequest_mergecsp.js, as for RImtaISARFC0EnQ1pRxOUg.
- Got: "TIMEOUT — 1 failing execution of 3 / Test timed out" with neither "Passed when the harness reran it" nor, for IYyCUf1YSa6jUovnDSBhMA, "Failed only in the parallel phase", although the resource-usage profile has the `retry` phase `PASS` (7.955 s). The "of 3" counts the `SKIP` from `xpcshell.toml` (the include is `run-if = android`) as an execution.
- Workaround: `thread markers --search name:test --list` on the resource-usage profile.
