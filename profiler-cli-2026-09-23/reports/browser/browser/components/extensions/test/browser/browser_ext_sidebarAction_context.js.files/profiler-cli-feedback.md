## Question: what was the machine's CPU use while one test ran (resource-usage profile)

- Command: `profiler-cli zoom push m-2 --session <s>` (the `test` marker) then `profiler-cli thread markers --session <s> --search "name:CPU Use" --list --limit 0`
- Expected: each row showing its CPU percent (the one number that answers "was the machine saturated").
- Got: rows with only name, time and duration (`CPU Use  t=13m37s  99ms`), no value. `profile info` says "No significant activity" and `counter list` says "No counters", so nothing else shows it either.
- Workaround: `--json` and a Python one-liner printing `data.cpuPercent` per marker. The list could print the marker's first field (CPU Percent) as its label, as it does for other marker types.

## Question: how late did each window timer callback run (review)

- Command: `profiler-cli thread markers --session <s> --search "setIdleCallback handler" --list --limit 0`, then `profiler-cli marker stack <m> --session <s>` once per marker to read `Captured at:`.
- Expected: for a `setTimeout callback` marker, the time the timer was set (its stack's capture time), next to the time it ran, so lateness = run − capture − interval can be read off the list.
- Got: the list shows only the run time. The capture time is only printed by `marker stack`, one marker per call (23 calls here).
- Workaround: a shell loop over the handles, with Python to subtract. A `Captured` column in `--list` for markers that have a stack would answer it directly.

## Question: which document did each DOMEvent fire on (review)

- Command: `profiler-cli thread markers --session <s> --search "name:DOMEvent" --list --limit 0` in a zoom range.
- Expected: each row showing the event's `innerWindowID`, to tell the sidebar's about:blank from its new document when the target is just `document`.
- Got: rows like `unload - document` with no window ID. It is only in `--json` `data.innerWindowID`.
- Workaround: `--json` and a Python filter by event type and innerWindowID.
