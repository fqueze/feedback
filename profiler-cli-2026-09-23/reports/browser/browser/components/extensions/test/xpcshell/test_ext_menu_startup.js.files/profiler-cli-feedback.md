# profiler-cli feedback

## Excluding one Runnable task name from a marker list
- Command: `profiler-cli thread markers --session <s> --search "name:Runnable,-DummyEvent" --list --limit 0`
- Expected: Runnables without the thousands of `DummyEvent` ones, or an error saying a bare `-term` is not an exclusion.
- Got: the DummyEvent rows anyway (the bare `-DummyEvent` was silently ignored or treated as a positive term), hundreds of lines of output.
- Workaround: `-name:DummyEvent` (the task name is the payload key `name`, which I found with `marker info --json`).

## Was the machine saturated while this test ran? (resource-usage profile)
- Commands: `profiler-cli profile info`, `profiler-cli counter list`, `profiler-cli thread markers --search "name:CPU Use" --list` on profile_resource-usage.json of task fHKIwkcoRSGJjDLlotw6yg, zoomed on the test marker.
- Expected: machine CPU over the zoomed range (a sparkline or per-marker cpuPercent/idle_pct column, as `counter info` does for counters).
- Got: `profile info` says "CPU activity over time: No significant activity", `counter list` says "No counters in this profile", and the `CPU Use` list shows only name/time/duration, no percentage. All three read as "no CPU data" although every CPU Use marker says 100% busy, 0% idle.
- Workaround: `--list --limit 0 --json` and a python one-liner over `fields[].cpuPercent` / `idle_pct`.


## When was the one sample that shows a given stack taken? (review, profile OcLywTlcQTq1eBEabVb1VQ)
- Command: `profiler-cli zoom push 3834.6ms,3840.1ms` then `profiler-cli thread samples` — one sample, `Task ChromeUtils::IdleDispatch > promise callback > updatePersistedMenus > Map.prototype.entries`.
- Expected: a way to get that sample's timestamp (e.g. `thread samples --list` with times, or the time in `--json`), to place it against a marker 1 ms later.
- Got: aggregated counts only; no sample time.
- Workaround: bisected the zoom range four times, counting `grep -c 'Map.prototype.entries'` in each half (sample in 3839.2–3839.6 ms).
