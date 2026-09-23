## Question: how many ms after a test started was its "will retry" logged, and which tests started in the same 200 ms?

- Command: `profiler-cli thread markers --search test_stepping-12 --list --limit 0 --session <s>` (resource-usage profile, 3m37s long)
- Expected: marker start times precise enough to order events that are milliseconds apart.
- Got: `t=2m14s` for the `test` marker and the `will retry` INFO, which are 33 ms apart; the same rounding for `thread markers --search name:test --list` within a 200 ms window. `zoom push` to 300 ms did not change the precision of the `t=` column.
- Workaround: `--json` and a Python script over `flatMarkers[].start` / `.duration`, also to count PASS vs TIMEOUT `test` markers starting after a given time.
- Could have shown: `t=` with ms precision when the view (or the listed markers' spread) is under a few seconds, or a `--time-precision` flag; and a status breakdown (`--group-by field:status`) for `test` markers in a zoomed range.
