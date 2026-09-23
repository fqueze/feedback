## Log markers (mochitest INFO) print "(empty)" for level and message

- Command: `profiler-cli thread markers --category Test --list --limit 0 --session browser-preview_highlight-1` on t-16 (content process of a mochitest-plain profile, task Sqwzc2-jSkKv9BtTq3pwsg run 1), and `profiler-cli marker info m-67`.
- Expected: `INFO  SimpleTest START` — the test's own info() log, which is the point of reading the Test category.
- Got: every `INFO` row is `[(empty)] INFO: (empty)`; `marker info` shows `Level: (empty)` / `Message: (empty)`. `--json` has `"value": "SimpleTest START"` with `"formattedValue": "(empty)"`, so the data is there and only the formatter drops it.
- Workaround: `--list --json` and print `fields[].value` with a script.


## (review) "CPU use of the machine over 75.6–76.1 s" needs one `marker info` per 100 ms marker

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session browser-review-preview_highlight-3` after `zoom push 75.2,76.6`, on a resource-usage profile (task Sqwzc2-jSkKv9BtTq3pwsg run 1).
- Expected: one row per sample showing its `CPU Percent`, with a start time precise enough to tell the rows apart.
- Got: rows with an empty description, and every start printed as `t=1m15s` / `t=1m16s`, so neither the value nor which 100 ms slot a row covers is visible. The Test markers in the same list (`INFO`, `PASS`, `FAIL`) are also all `t=1m16s`, so they cannot be ordered against the CPU rows.
- Workaround: `marker info m-N --json` per handle, reading `start` and `fields[cpuPercent].formattedValue`. Showing `cpuPercent` in the row and millisecond starts past 1 minute would answer this directly.
