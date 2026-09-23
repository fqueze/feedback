## Question: how long after the test started did the harness log "will retry"? (42 ms here, the key fact)

- Command: `profiler-cli thread markers --session <s> --search objectgrips-sparse-array --list --limit 0`
- Expected: start times precise enough to compare markers a few ms apart.
- Got: `t=2m13s` for both the `test` marker and the `INFO ... will retry` marker, so no way to tell 42 ms from 900 ms.
- Workaround: `--json` and a script reading `flatMarkers[].start`.
- What would have answered it: millisecond start times in `--list` output (at least when the profile is under an hour), or a `--precise` flag.

## Question: what was the machine's CPU use while this test was running?

- Command: `profiler-cli zoom push 133.3,178.4` then `profiler-cli thread markers --search "CPU Use" --list --limit 0` on a resource-usage profile (`counter list` says "No counters in this profile").
- Expected: an average / min / max of `cpuPercent` over the zoomed range.
- Got: 452 individual markers.
- Workaround: `--json` and a script averaging `data.cpuPercent`.
- What would have answered it: a summary of numeric payload fields (mean/min/max) in the aggregated `thread markers` view, or exposing the resource-usage CPU markers as a counter so `counter info` works.
