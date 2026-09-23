## Question: what was the whole machine's CPU use during one test, from the resource-usage profile?

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --session <s>` (after `zoom push` on the test's `test` marker)
- Expected: one row per sample with its CPU %, as the list does for other payload-bearing markers (e.g. DOMEvent's event type).
- Got: rows with only time and duration (`m-420  CPU Use  t=3m2s  93ms  ✗`), no percentage; `counter list` says "No counters in this profile".
- Workaround: `--json` and a Python script printing `fields.cpuPercent` / `user_pct` / `system_pct` per marker. The list row could show "CPU 58.9% (user 31.5%, sys 27.3%)", or these could be exposed as a counter with `counter info` over-time buckets.

## Question: which marker does this link's `marker=N` point to? (review of the report's links)

- Command: `profiler-cli load "<profiler.firefox.com URL with ...&marker=7154&thread=a>" --session <s>`, then `profiler-cli marker info m-325 --session <s>`
- Expected: `load` to say which marker the URL selects (handle and name), or `marker info` to print the marker's index, or a way to look a marker up by index (`marker info --index 7154`).
- Got: `load` reports only the selected thread; plain `marker info` has no index line. Checking each of 20 links meant finding a candidate with `thread markers --search`, then `marker info <m> --json | python3 -c '...markerIndex...'` per handle.
- Workaround: the `--json` + script loop above. An `Index: 7154` line in `marker info`, or the selected marker in `load`'s status, would make this one command.
