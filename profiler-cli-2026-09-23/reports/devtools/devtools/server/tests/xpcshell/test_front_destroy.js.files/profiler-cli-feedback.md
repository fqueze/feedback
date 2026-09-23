## `thread markers --list` drops sub-second precision past one minute

- Command: `profiler-cli thread markers --search test_front_destroy --list --limit 0 --session test_front_destroy.js-3` (task ZtblArCLSx6O6KzfEkL1YQ)
- Expected: start times precise to the ms, as below one minute (`t=46.531s`).
- Got: `t=1m5s` for both the test's `test` marker and its "will retry" INFO line, so the gap between them (tens of ms elsewhere, and the evidence here) cannot be read. Same for LKys8j8iTQKTAN9hKv040g (`t=2m13s`).
- Workaround: none; reported the gap only for the jobs where the times fall under one minute. `--json` would have had it.
