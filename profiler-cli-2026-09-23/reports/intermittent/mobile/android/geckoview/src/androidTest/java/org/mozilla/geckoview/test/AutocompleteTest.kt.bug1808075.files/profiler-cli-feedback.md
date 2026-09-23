## Question: "what was the machine's CPU use over this range?"

- Command: `profiler-cli thread markers --session AutocompleteTest.kt.bug1808075-1 --search "name:CPU Use" --list --limit 0` (resource-usage profile, zoomed to a 30 s test wait)
  - Expected: `cpuPercent` per row, or a min/median/max of it for the range.
  - Got: handles, times and durations only. The `--json` form has `durationStats` but no payload values, and the profile has no counters (`counter list`: "No counters in this profile").
  - Workaround: scraped the handles from the list, passed all 304 of them to `marker info`, grepped `cpuPercent` and computed the median in Python.
  - What would have answered: a payload-field aggregation on `thread markers`, e.g. `--stats field:cpuPercent`, or showing numeric payload fields in `--list` rows.
