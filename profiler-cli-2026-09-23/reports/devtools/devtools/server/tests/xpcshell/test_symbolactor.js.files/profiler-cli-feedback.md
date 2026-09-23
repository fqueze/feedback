## `thread markers --list` drops sub-second precision past one minute

- Command: `profiler-cli thread markers --session <s> --search test_symbolactor --list --limit 0` on ZtblArCLSx6O6KzfEkL1YQ's resource-usage profile
- Expected: start times with milliseconds, as below 60 s (`t=46.930s`).
- Got: `t=1m6s` for both the test's start and its "will retry" INFO, so the 30-70 ms between them, which is the evidence here, cannot be read. Same in LKys8j8iTQKTAN9hKv040g (`t=2m14s`).
- Workaround: `--json` and read `start`; or zoom to a small range, where the listing still prints `1m32s`.
