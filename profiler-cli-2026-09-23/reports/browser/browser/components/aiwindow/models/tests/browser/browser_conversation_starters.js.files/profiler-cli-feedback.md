## Question: what were the machine's CPU, iowait and disk write rate during a 5 s window of a resource-usage profile?

- Command: `profiler-cli thread markers --search "name:CPU Use,name:IO" --list --limit 0 --session <s>` on a `profile_resource-usage.json`.
- Expected: each `CPU Use` row showing its CPU % / IO Wait % / Idle %, and each `IO` row its write_bytes, since those fields are the whole point of the marker.
- Got: rows with only name, time and duration (`m-411  CPU Use  t=1m58s  100ms  ✗`), no values. `marker info` shows them, but one marker at a time; 10 markers per second of profile.
- Workaround: `--list --json` and a Python script over `flatMarkers[].data` to tabulate iowait and bucket write_bytes per second.
- What the default output could have shown: the marker's schema "table label"/key fields inline, as for Text markers; or a `counter`-like summary of these resource markers over the zoom range (mean/max CPU and iowait, total bytes written).

## Question: at what time in the job's resource-usage profile did per-test t=X happen? (review)

- Command: `profiler-cli thread markers --search "conversation_starters" --list --limit 0 --session <s>` on `profile_resource-usage.json` (21 min long).
- Expected: marker times precise enough to align the per-test profile with the resource timeline (sub-100 ms: the CPU Use / IO markers are 100 ms apart).
- Got: `t=1m48s`, `t=1m51s` — times rounded to the second once the profile is over a minute long, so the offset between the two profiles could not be read.
- Workaround: `--list --json` and a script over `start`, matching the `TEST-UNEXPECTED-FAIL` marker present in both profiles (offset 88.104 s).
- What the default output could have shown: millisecond times (`t=108.247s`) whatever the profile length, or the per-test profile's `startTime` as an absolute offset so the two can be aligned directly.
