# profiler-cli feedback

## Question: how busy was the machine over time in a resource-usage profile, and is there a constant floor (a pegged core)?

- Command: `profiler-cli counter list --session toggle_usb_devices-ru` on `profile_resource-usage.json` of task CpHh9VmXQ9KMzYSzxG30oQ
- Expected: the machine CPU as a counter, so `counter info` with `zoom push` gives per-phase averages and the "over time" buckets.
- Got: `No counters in this profile.` The CPU data is 28,000 `CPU Use` interval markers (one per 100 ms, `cpuPercent`, `user_pct`, `system_pct` fields), and `thread markers --search "name:CPU Use"` only gives counts and durations, not field statistics.
- Workaround: `thread markers --search "name:CPU Use" --list --limit 0 --json`, then a Python script for per-window mean and p10/p50/p90 and per-20 s floors. A field aggregate (`--group-by`-like `--stats field:cpuPercent` with time buckets), or exposing these markers as a counter, would have answered it directly.

## Question: were there any TCPSocket `open` or `data` events?

- Command: `profiler-cli thread markers --search "eventType:open,eventType:data" --list --limit 20 --session toggle_usb_devices-1`
- Expected: only DOMEvents whose eventType is exactly `open` or `data`.
- Got: two `TabOpen` DOMEvents; `field:value` is a substring match with no exact-match form, so a short event name cannot be isolated.
- Workaround: `thread markers --search DOMEvent --group-by field:eventType` and reading the whole list for `open`/`data`.
