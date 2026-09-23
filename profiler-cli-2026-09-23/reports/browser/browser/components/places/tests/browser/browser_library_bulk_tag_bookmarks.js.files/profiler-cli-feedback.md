## Samples commands keep using the last popped/cleared zoom range

- Command: `profiler-cli zoom push 139.0,139.6 --session S` → `zoom clear` → `thread samples --session S` (header says `View: Full profile`).
- Expected: samples over the whole 9m26s profile (the thread has 20006 samples).
- Got: `Categories (9 running samples)`, `38` with `--include-idle` — the 0.6 s range I had cleared. Repeating with `zoom push 130,145` → `zoom pop` → `thread samples` gives `651 running samples`, again the popped range, while the header still says `Full profile`. Markers commands are not affected.
- Workaround: `zoom push 0,<end>` explicitly before `thread samples` (gives 19984 samples).
- Cost: I nearly concluded that the per-test profile had lost its samples.

## Machine CPU over time from a resource-usage profile

- Question: was the machine saturated during a 10-minute window of the job?
- Command: `profiler-cli counter list` on `profile_resource-usage.json` → `No counters in this profile.`; the CPU data is in 27,762 `CPU Use` markers, so I had to dump `thread markers --search "name:CPU Use" --list --limit 0 --json` and bucket `cpuPercent` per 30 s in Python.
- What would have answered it: `counter info`-style "over time" buckets for the `CPU Use` markers, or `thread markers --search "name:CPU Use" --group-by time:30s` with mean/max of a numeric field.
