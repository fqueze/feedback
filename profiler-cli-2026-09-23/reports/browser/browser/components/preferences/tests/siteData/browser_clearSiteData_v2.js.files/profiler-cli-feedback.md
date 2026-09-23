## Question: "was the machine I/O-bound (disk writes, IO wait) during this window?" (resource-usage profile)
- Command: `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0` (and `name:IO`, `name:Memory`)
- Expected: each row showing its values (CPU %, IO wait %, write_bytes/read_bytes, used/cached), or a summary per second over the zoom.
- Got: rows with only name/time/duration; the values need `marker info` one by one.
- Workaround: `--json | jq '.flatMarkers[] | .data'` then awk to bucket per second. Cost ~6 commands per profile.
- What could have shown it: the payload fields inline in `--list`, or `counter`-like stats for these marker types (the resource profile has "No counters").

## Question: "what started in this time window?" (zoomed `thread markers --list`)
- Command: `profiler-cli zoom push 16.9,17.13; profiler-cli thread markers --thread t-0 --list --limit 0`
- Expected: markers that happen in the window.
- Got: ~150 long IPCIn/IPCOut and CSS animation markers that started at t=1.6s–5s and merely overlap the window, before the 30 relevant ones.
- Workaround: long `--search "-name:IPCIn,-name:IPCOut,-name:CSS animation,..."` exclusion list.
- What could have shown it: a `--starts-in-range` option, or sorting overlapping-from-before markers last.

## Question: "when did each process start and end?"
- Command: `profiler-cli profile info --all`
- Got: `[ts-C5 → ts-M]` handles instead of times.
- Workaround: `profile info --all --json | jq '.processes[] | .startTime,.endTime'`.
