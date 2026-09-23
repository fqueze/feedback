## Question: how busy was the machine (CPU and I/O wait) over the seconds a test ran, in a resource-usage profile?

- Command: `profiler-cli profile info --session <s>` on `profile_resource-usage.json` (xpcshell job Kq_shcStTCarlGXkbH3OpA)
- Expected: a CPU/IO-wait summary over time, like per-test profiles show "CPU activity over time".
- Got: "No significant activity." and `counter list` says "No counters in this profile", although the profile holds one `CPU Use` marker per 100 ms with cpuPercent / iowait_pct / idle_pct fields.
- Workaround: `thread markers --search "name:CPU Use" --list --json` to get handles, then `marker info <all handles> --json` piped through python to print start, cpuPercent, iowait_pct, idle_pct per marker.
- What could have answered it: `profile info` (or a `thread markers --group-by field:...` / a "--fields" column option on `--list`) summarising the CPU Use markers' fields over the zoomed range, e.g. mean/max CPU and IO wait per second.

## Question: which link `marker=N` index does each of these marker handles have? (review of test_ext_dnr_dynamic_rules.js)

- Command: `profiler-cli marker info m-7 m-2 m-28 m-61 --session <s>` on a resource-usage profile (VyT06-PBQc6BaRt9fsJh3g), to check a report's links.
- Expected: the marker index next to each handle, since profiler.firefox.com links use it.
- Got: the default output shows name, time and fields, but no index. Only `--json` has `markerIndex`.
- Workaround: `marker info <handles> --json | python3 -c ...` to print `markerIndex`, start and duration per handle. To identify an unknown `marker=5549`, I dumped `marker info m-51..m-70 --json` and scanned for the index.
- What could have answered it: showing the index in `marker info` and `--list` rows, or a `marker find --index 5549` that returns the handle.
