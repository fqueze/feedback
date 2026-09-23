## Marker times past 60 s lose their precision in `--list` output

- Command: `profiler-cli thread markers --category Test --search telemetry-debug-worker --list --limit 0 --session tdw-central-4` (profile ToEZ4g71QiCpjvew7Jqmfg.0, test running at 76-84 s)
- Question: at what time, to the millisecond, did each step of the test happen, and which came first between the click and the SW's registration?
- Expected: `t=80.688s`, as for markers under 60 s.
- Got: `t=1m20s`, `t=1m21s`, `t=1m23s` for every row: steps 30 ms apart print the same time, and `zoom push 76,84.5` did not change the format.
- Workaround: `--json` and a Python script to print `start/1000`. The same happens with `profile markers --search` across threads.

## Finding when the machine's disk was saturated in a resource-usage profile

- Command: `profiler-cli thread markers --search "name:IO" --list --limit 0 --session tdw-central-ru1 --json` then a script grouping `write_bytes > 5 MB` per 100 ms sample into runs.
- Question: was the disk saturated during a given interval, and when did the heavy writing start and stop?
- `IO` and `CPU Use` are markers, not counters, in resource-usage profiles, so `counter info` has nothing, and `--list` prints one row per 100 ms sample without the `write_bytes`/`iowait_pct` values.
- What would have answered it: `counter list`/`counter info` exposing CPU Use, IO wait and IO write bytes of resource-usage profiles as counters (with the per-bucket "over time" view), or `--list` printing those payload fields.
