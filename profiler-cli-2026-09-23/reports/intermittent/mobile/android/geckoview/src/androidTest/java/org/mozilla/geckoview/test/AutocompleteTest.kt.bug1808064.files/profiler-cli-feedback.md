## Question: "what was the machine's CPU use during this test's wait?"

- Command: `profiler-cli thread markers --session AutocompleteTest.kt.bug1808064-1 --search "name:CPU Use" --list --limit 0` (resource-usage profile, zoomed to the test)
- Expected: `cpuPercent` per row, or a min/median/max for the range.
- Got: handles, times and durations only.
- Workaround: the same command with `--json` carries each marker's `data.cpuPercent`; a few lines of Python gave the median and max. A `--stats field:cpuPercent` or numeric payload columns in `--list` would have answered it directly.

## (review) `cpuPercent` in `--json` is a formatted string

- Command: `profiler-cli thread markers --session review-AutocompleteTest.kt.bug1808064-1 --search "CPU Use" --list --limit 0 --json` (resource-usage profile of cZNJf2x0, zoomed to the test)
- Question: same as above, the machine's CPU use during the wait.
- Expected: `data.cpuPercent` as a number.
- Got: a string such as `"30.8%"`, so a numeric comparison in the script failed until the `%` was stripped.
- Workaround: `float(v.rstrip('%'))`. A numeric raw value next to the formatted one, or the `--stats` suggested above, would avoid it.
