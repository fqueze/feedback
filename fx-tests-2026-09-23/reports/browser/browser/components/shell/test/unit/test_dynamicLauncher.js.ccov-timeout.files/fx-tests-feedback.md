## The ccov fail rate and durations mix in the harness's sequential retries

- Command: `fx-tests test browser/components/shell/test/unit/test_dynamicLauncher.js` and `... --durations`.
- Expected: the rate at which the test fails when it runs normally (parallel phase), and how long a normal run takes.
- Got: "test-linux2404-64-ccov/opt-xpcshell-nofis 47.3%", and ccov durations "median 26854, p95 113853–122442" from passing runs. Only `--executions --config ...-nofis` shows the parallel rate is 89.7% (61/68): 61 of the 129 runs are the sequential retries that always pass. The median 26.9 s is the retry's time; the 16 parallel passes are the ~113–123 s tail, right at the 120 s timeout.
- What could show it: a parallel-only rate column in the per-config table, and durations split by execution mode.

## `--executions --config test-linux2404-64-ccov/opt-xpcshell` also counts `-nofis`

- Command: `fx-tests test <path> --executions --config test-linux2404-64-ccov/opt-xpcshell`
- Expected: 56 failures (that config only).
- Got: 117 failures, 133 parallel runs: the sum of `ccov/opt-xpcshell` and `ccov/opt-xpcshell-nofis`. The `-nofis` config alone gives 61/68, so the filter is a prefix match.
- Workaround: subtract the `-nofis` numbers.

## A passing counterpart job has no command

- Question: "a job on this config where this test passed in the parallel phase" (for its resource-usage profile).
- Commands: the Treeherder REST API (`/api/project/mozilla-central/push/?revision=`, then `/api/jobs/?push_id=`) to list the push's ccov xpcshell jobs, then `fx-tests task <id> --passed --limit 0 | grep test_dynamicLauncher` on each of them.
- What could show it: `fx-tests test <path> --task-ids --passed` (or `--status PASS --mode parallel`).
