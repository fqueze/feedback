## Question: which code dirtied style / changed element state in this time range?

- Command: `profiler-cli thread markers --search SetNeedStyleFlush --list --limit 0 --session <s>` (zoomed to a 12 ms range)
- Expected: each row to show the top frames of its stack (e.g. `ElementStateChanged ← Node.appendChild ← populateSubView`), or a `--group-by stack` that buckets them.
- Got: rows only show `✓` (has stack) and no payload text, so every row looks the same.
- Workaround: `--json | jq -r '.flatMarkers[].handle'`, then a loop of `marker stack <m>` per handle, then `sort | uniq -c` on the first frames. `marker info m-a..m-b` does include stacks, but it prints ~20 lines per marker.
- What would have answered it: an option on `thread markers --list` that shows the first 2–3 stack frames inline, or a way to group by the leaf/first JS frame of the marker stack.
