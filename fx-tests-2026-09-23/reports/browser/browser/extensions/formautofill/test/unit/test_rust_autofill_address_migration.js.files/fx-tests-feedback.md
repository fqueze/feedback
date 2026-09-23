## `--config` matches as a prefix, silently merging variants
- Command: `fx-tests test <path> --executions --config test-linux2404-64-artifact/debug-xpcshell`
- Expected: the fission config only (60 failures in the per-config table).
- Got: 106 failures, i.e. debug-xpcshell plus debug-xpcshell-nofis, with no note that two configs matched.
- Workaround: reason from the per-config table instead.

## Question: are a config's passes only the sequential retries?
- `fx-tests test <path>` shows linux2404-64-artifact/debug at 50.0% (60/120). Only `--durations` (passes at 5.3-7.0 s vs. 33 s timeouts) and `--executions` hinted that all 60 passes were retries, i.e. 100% of parallel runs fail there.
- Could have shown: a per-config parallel-phase failure rate next to the overall rate in the default table.
