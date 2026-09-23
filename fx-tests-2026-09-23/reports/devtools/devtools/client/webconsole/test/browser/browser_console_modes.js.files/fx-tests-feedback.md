## Question: which tasks ran this test on a try push (to link a passing run)

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --all-jobs --test devtools/client/webconsole/test/browser/browser_console_modes.js --task-ids --profiles` (and the same with `--json`)
- Expected: the per-config table plus the task IDs (and resource-usage profile URLs) of the jobs counted in each row, since `--task-ids`/`--profiles` were passed.
- Got: only the counts table (jobs/passed/passed on retry/failed); `--task-ids` and `--profiles` are silently ignored in `--test` mode, and the JSON has no task IDs either.
- Workaround: grepped the task IDs of the same config out of the default `fx-tests try --task-ids` output (only present because other tests failed in those jobs), then `fx-tests task <id> --passed` to confirm the test ran there.

## Question: did the manifest-level ASan leak (the `skip-if` reason) reproduce on this try push

- Command: `fx-tests task SG5h3qzRTDi60GlyHb25Sg --passed --limit 0`, and `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --limit 0`
- Expected: the job's `TEST-UNEXPECTED-FAIL | LeakSanitizer leak at XPCNativeInterface::NewInstance, ... | devtools/client/webconsole/test/browser/_browser_console.toml` failures listed (attributed to the manifest), since they turn the job orange and are exactly what the removed `skip-if = ["asan"]` (Bug 1910261) was about.
- Got: `fx-tests task` says "2 failing" and lists only two tests; neither command mentions `_browser_console.toml` or LeakSanitizer at all.
- Workaround: loaded the job's resource-usage profile and ran `profiler-cli profile markers --search LeakSanitizer`, which shows 9 `ERROR` markers for the manifest at t=23m23s.
