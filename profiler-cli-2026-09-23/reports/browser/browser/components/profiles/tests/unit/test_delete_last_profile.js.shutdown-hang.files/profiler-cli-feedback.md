# profiler-cli feedback (test_delete_last_profile.js.shutdown-hang)

## Question: what was the machine's CPU use, second by second, over this window of the resource-usage profile?
- Command: `profiler-cli counter list` (said "No counters in this profile"), then `thread markers --search "name:CPU Use" --list --limit 0 --json` piped to a Python script averaging `data.cpuPercent` per second.
- Expected: the CPU Use markers summarized over the zoom (min/avg/max, or per-bucket like `counter info`'s "over time").
- Got: only a flat list of 140+ markers, one per 50-280 ms sample.
- Workaround: script over `--json`.

## Question: exact times of a handful of markers in a 30-minute profile
- Command: `thread markers --search test_delete_last_profile.js --list --limit 0`
- Expected: times precise enough to order markers within a second.
- Got: `t=12m20s` for everything in that second (warning, exiting test), so the order and gaps were invisible.
- Workaround: `marker info m-1 m-45 ... --json` and a script reading `start` (and `markerHandle`; note `thread markers --json` calls the same field `handle`, which cost a retry).
