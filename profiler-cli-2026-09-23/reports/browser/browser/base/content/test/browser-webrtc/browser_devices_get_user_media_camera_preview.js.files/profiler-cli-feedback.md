## Question: how many ms pass between a test's INFO step and the crash line after it?

- Command: `profiler-cli thread markers --session <s> --search camera_preview --list --limit 0` on a 22-minute resource-usage profile
- Expected: timestamps precise enough to order and space log lines that are a few ms apart, e.g. `t=159.519s`.
- Got: `t=2m39s` / `t=2m40s` for every marker in the test. Both "Close permission prompt" and the `Hit MOZ_CRASH` line show as `t=2m40s`, so the 13 ms gap, and whether the crash came before or after a step, cannot be read.
- Workaround: `--json` and reading `flatMarkers[].start` with a Python one-liner.
