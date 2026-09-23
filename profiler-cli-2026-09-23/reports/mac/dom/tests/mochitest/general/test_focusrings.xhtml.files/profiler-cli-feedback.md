## Question: what was the machine's CPU use over a time range, from a resource-usage profile?

- Command: `profiler-cli counter list --session <ru>` says `No counters in this profile.`; the machine CPU is only in `CPU Use` markers (one per ~100 ms, `cpuPercent` field). `thread markers --search "name:CPU Use"` gives counts and durations, not the values.
- Needed: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json | python3 ...` to get mean/median/max of `cpuPercent` over a zoom.
- What would have answered it: an aggregate of a numeric marker field over the view (mean/max per field), or exposing these markers as a counter.

## `--search eventType:focus` cannot select only `focus` events

- Command: `profiler-cli thread markers --search "eventType:focus" --list`
- Expected: `focus` DOMEvents only (there were none).
- Got: the 9 `framefocusrequested` events, because `field:value` is a substring match. Telling "no focus event at all" from "only framefocusrequested" took reading the list.
- Would help: an exact-match form (e.g. `eventType:=focus`).
