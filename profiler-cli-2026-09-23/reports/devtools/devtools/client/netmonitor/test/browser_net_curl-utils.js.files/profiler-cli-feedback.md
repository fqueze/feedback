## Question: was the machine saturated while this one test ran? (resource-usage profile)

- Command: `profiler-cli counter list --session <s>` on `profile_resource-usage.json` (task Dnc4oLx1SF2YmuWFLide9Q), after `zoom push m-11` (the `test` marker of the test).
- Expected: a CPU counter/track whose stats honour the zoom, as `counter info` does for Process CPU in Gecko profiles.
- Got: `No counters in this profile.` Machine CPU exists only as 911 `CPU Use` markers with a `cpuPercent` string field; `thread markers --search "name:CPU Use"` gives counts and durations but no min/avg/max of the field.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` piped to Python to average `data.cpuPercent` (2.0% avg, 60.4% max over the test's 1m31s).
- What would have answered it: field statistics (avg/max/percentiles) for numeric marker payload fields in the aggregated `thread markers` output, or exposing the resource-usage CPU markers as a counter.
