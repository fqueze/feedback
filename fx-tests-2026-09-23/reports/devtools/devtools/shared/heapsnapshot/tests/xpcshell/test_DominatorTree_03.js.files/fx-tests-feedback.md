## Question: which harness message is behind "Failure details not recorded" in each failing run?

- Command: `fx-tests test devtools/shared/heapsnapshot/tests/xpcshell/test_DominatorTree_03.js --task-ids --limit 0`, then `fx-tests task <id> --profiles`.
- Expected: per run, the harness's own lines for the test (here `Failed wait for remote log: … missing?` in URr5GA6zTWyWd67WrPmjrQ, `Failed to start process: … Could not kill left-over process` in bIsp8wAZT9aDbvcAq1uo1A).
- Got: both runs grouped as one issue, "Failure details not recorded (likely Android or platform logging issue)", and `task` shows only `FAIL — 1 failing execution of 2`. The two runs are two unrelated mechanisms.
- Workaround: downloaded `public/logs/live_backing.log` and grepped it (or `profiler-cli thread markers --search <test>` on the resource-usage profile).
- Could have shown: on Android, the `remotexpcshelltests.py | …` / `WARNING` lines naming the test, as the failure message for that execution.

## `--bugs` with no match prints nothing

- Command: `fx-tests test <path> --bugs`.
- Expected: a line such as "Bugs: none found naming this test".
- Got: the normal output with no bug section, so "searched, found none" looks the same as "flag ignored".
