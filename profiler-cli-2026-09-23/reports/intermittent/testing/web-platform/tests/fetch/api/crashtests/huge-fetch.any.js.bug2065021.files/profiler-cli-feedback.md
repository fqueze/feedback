## review-huge-fetch.any.js.bug2065021: which marker index a link's `marker=N` points at

- Question: does the report's link `marker=48581` point at the marker it quotes?
- Command: `profiler-cli marker info m-2 --session <s>` (also with several handles)
- Expected: the marker's `markerIndex` in the default output, so a link can be checked without extra steps.
- Got: the index shows only in `--json`. I needed `marker info <m> --json | python3 -c ...` for each of the 6 markers.
- Could show: a `Index: 48581` line in `marker info`, or an index column in `thread markers --list`.
