## Marker times of 60 s and more are rounded to the minute

- Command: `profiler-cli thread markers --category Test --search breadcrumbs_visibility --list --limit 0 --session browser_inspector_breadcrumbs_visibility.js-1`, then `profiler-cli marker info m-11 --session …`
- Expected: the timeout marker's time to the millisecond (60.341s), like the rows before it (`t=15.147s`).
- Got: `t=1m` in the list and `Time: 1m (instant)` in `marker info`; in the resource-usage profile, `t=16m26s` for every marker in the same second, so the order of the test's markers there cannot be read.
- Workaround: `marker info m-11 --json` and read `start`.

## Resource-usage profile: "was the machine busy at t?" needs a script

- Command: `profiler-cli profile info` / `counter list` on `profile_resource-usage.json`, then `thread markers --search "name:CPU Use,name:Memory" --list`
- Expected: some way to see the CPU and memory values around one time.
- Got: `profile info` says "No significant activity", `counter list` says "No counters in this profile", and the marker list shows `CPU Use … 94ms` rows with no value. The values appear only in `marker info`, one marker at a time.
- Workaround: `--list --json` piped into python to print `cpuPercent` / `used` for each marker.
