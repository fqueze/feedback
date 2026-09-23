## Machine CPU % over a time range, in a resource-usage profile
- Commands: `profiler-cli counter list` gives "No counters in this profile". `profile info` gives "No significant activity" and 0 ms CPU. `thread markers --search "name:CPU Use" --list` gives rows without the CPU % fields.
- Question: "what was machine CPU (user/system) during this 45 s test marker, compared with before and after it?"
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script bucketing `fields[].user_pct` per 5 s. Same for `Memory` (`data.used`).
- Would have answered it: a numeric summary of marker payload fields over the current zoom (mean/min/max per field, optionally bucketed), for example `thread markers --search "CPU Use" --stats [--bucket 5s]`. Or a `profile info` for resource-usage profiles that summarizes these markers instead of reporting no activity.
