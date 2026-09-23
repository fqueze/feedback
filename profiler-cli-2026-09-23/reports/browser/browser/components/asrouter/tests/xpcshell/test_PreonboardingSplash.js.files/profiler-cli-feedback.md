## How busy was the machine, minute by minute, in a window of a resource-usage profile?

- Command: `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0` (after `zoom push 1200,1440`)
- Expected: a per-interval summary of the `CPU Use` markers (mean cpuPercent / user / system per N seconds), since this profile has no counters (`counter list` → "No counters in this profile").
- Got: 1,720 individual markers in 4 minutes, one row each; the text output shows no values, only names and durations.
- Workaround: `--json` and a Python script to bucket `data.cpuPercent` per 10 s / per minute. Same need for `Memory` and `IO` markers.
- Also: `idle_pct` in the CPU Use payload reads "32.2%" in the first markers while cpuPercent is 68.3% and user 65% / system 5% — the fields do not add up.

## Were all tests slow during this period, or only mine?

- Command: `profiler-cli thread markers --session S --search "name:test" --list --limit 0`
- Expected: a way to aggregate the `test` interval markers' durations over time (median duration per minute), which is what shows a machine-wide stall.
- Got: one row per test (2,708 in the job); reading which ones overlapped a window required `zoom push` and scanning by eye.
- Workaround: `--json` + script to compute per-minute median duration and CRASH counts.
