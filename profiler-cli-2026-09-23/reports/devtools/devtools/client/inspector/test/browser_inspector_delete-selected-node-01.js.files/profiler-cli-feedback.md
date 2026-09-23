## `--search` splits on commas, and Windows log lines put commas in pids

- Command: `profiler-cli thread markers --session <s> --search "Child 9,120" --list` on a resource-usage profile
- Expected: the log lines from `[Child 9,120: ...]` (the log writes pids with a thousands separator).
- Got: 902 markers matching "Child 9" OR "120", mostly Memory/CPU markers.
- Workaround: searched `9120` and `pid9120` instead. A way to quote or escape a comma inside a term would fix it.
