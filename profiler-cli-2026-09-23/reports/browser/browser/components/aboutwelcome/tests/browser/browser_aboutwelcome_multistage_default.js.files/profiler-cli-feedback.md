## Marker times lose sub-second precision past one minute

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0 --session <s>` and `profiler-cli marker info m-79 m-80 --session <s>`
- Expected: times like `t=62.113s`, precise enough to order markers a few ms apart (the default output for t<60s is `t=53.542s`).
- Got: `t=1m2s` for every marker between 62.0 and 63.0s, so "Entering test", the content TypeError 115 ms later and the TEST-PASS lines in between all read the same; `marker info` also prints `Time: 1m2s`.
- Workaround: `marker info ... --json` and read `.markers[].start` (ms).

## `--min-duration 0` silently drops every instant marker

- Command: `profiler-cli thread markers --session <s> --search "name:ReceiveQuery" --min-duration 0 --list --limit 3` (in a zoom holding 19 such instant markers)
- Expected: the 19 markers; a zero minimum should be a no-op, and I added it hoping to keep the long `Runnable`s next to the instant JSActor markers in one list.
- Got: `0 markers ... No markers match the specified filters.` — no hint that instants were excluded. Without `--min-duration` the same query lists them.
- Workaround: drop `--min-duration` and filter durations myself over `--json`.

## `marker info --json` changes shape with the number of handles (review)

- Command: `profiler-cli marker info m-197 --session <s> --json` after `profiler-cli marker info m-1..m-40 --session <s> --json`
- Expected: the same shape both times, so one script reads either.
- Got: several handles give `{"markers": [...]}`; one handle gives the marker object itself at top level, and my script died on `KeyError: 'markers'`.
- Workaround: `d.get('markers') or [d]`.
