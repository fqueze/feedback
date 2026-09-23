## Question: machine-wide CPU use and IO wait during one test's window (resource-usage profile)

- Commands: `profiler-cli profile info`, `profiler-cli counter list`, then `zoom push m-44` + `thread markers --search "name:CPU Use" --list`
- Expected: CPU %, IO wait % (and IO read/write bytes) over the zoomed range, e.g. averaged or bucketed, since in mozharness resource-usage profiles these are carried by `CPU Use` / `IO` markers.
- Got: `profile info` says "CPU activity over time: No significant activity."; `counter list` says "No counters in this profile."; the marker list shows one row per 100 ms marker with no field values.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json` and `--search name:IO`, then a script bucketing `data.iowait_pct`, `data.cpuPercent`, `data.read_bytes`, `data.write_bytes` per 5-10 s.
- What would have answered it: a `--group-by`/`--aggregate` over a numeric payload field for the zoomed range (avg/min/max of `iowait_pct`), or treating these markers as counters in `counter info`.

## Review (review-aria_autocomplete_inline_list_both.py): the same question again

- Checking this report's IO-wait figures needed the same `--json` + bucketing script over `CPU Use` / `IO` markers, for 5 profiles. Also needed: the profile's zero as a wall-clock time, to line up log timestamps (derived here from a `test` marker's start vs its `TEST-START` log line).
