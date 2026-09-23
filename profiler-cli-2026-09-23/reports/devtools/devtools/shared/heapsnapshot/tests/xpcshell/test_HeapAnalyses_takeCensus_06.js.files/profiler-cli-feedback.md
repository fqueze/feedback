## `field:value` search does not match Test markers' status, but `--group-by field:status` does

- Command: `profiler-cli thread markers --session <s> --search "status:PASS" --list` (zoomed to 52.3s–end of a resource-usage profile)
- Expected: the `test` markers whose Status is PASS (or an empty list only if there are none).
- Got: "No markers match", while `--search "name:test" --group-by field:status` in the same zoom groups them fine (PASS/TIMEOUT/SKIP counts). `--search "PASS — "` (the text as printed) also matched nothing.
- Workaround: `--group-by field:status`. Cost: two wasted queries and doubt about whether the zero was real.

## Question: "which service slot / test file does each launch_application line belong to"

- Command: `profiler-cli thread markers --session <s> --search launch_application --list --limit 0`
- The list row cuts the message at a fixed width, before both discriminators (`XpcshellTestRunnerService$iN` is kept, but `_TEST_FILE = [...]` near the end is cut). I had to run `marker info` per handle in a shell loop and grep the output.
- What could have shown it: a way to widen or not truncate list rows (like fx-tests' COLUMNS / --full-messages), or a `--fields`/grep-within-payload option for `--list`.
