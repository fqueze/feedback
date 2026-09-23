## Question: in what order, to the millisecond, did the test's log lines and the network markers happen?

- Command: `profiler-cli thread markers --category Network --list --limit 0 --session <s>` (and the same with `--category Test`)
- Expected: a start time precise enough to order events a few ms apart (e.g. `t=103671.99ms`), since mousedown, SpeculativeConnect, ProcessNewTransaction, the TLS server's "Accepted" log and OnStartRequest all fall within 20 ms.
- Got: `t=1m44s` for every row, so the list cannot order them.
- Workaround: `--json` piped through a python script printing `flatMarkers[].start`, then `sort -n` across the two categories.
- What would have answered it: a millisecond start column in `--list` (at least when the view is zoomed under a few seconds), and a way to list two categories in one list.

## Network marker payload times are on another base than the marker's own start

- Command: `profiler-cli marker info m-25` on a `Load 619` Network marker.
- Expected: `startTime`, `domainLookupStart`, `connectStart`, ... on the same timeline as the `t=` of other markers.
- Got: raw payload `startTime: 103678.01` while the marker's start in `--list --json` is `103670.04` (about 8 ms apart, 7 ms on another profile), so the phase timestamps cannot be compared with other markers without computing the offset by hand.
- Workaround: subtract `payload.startTime - marker.start` from every phase field.
