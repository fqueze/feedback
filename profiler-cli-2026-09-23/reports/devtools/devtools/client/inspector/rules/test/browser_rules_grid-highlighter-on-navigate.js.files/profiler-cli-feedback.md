# profiler-cli feedback

## When, to the millisecond, did each test log line happen?

- Command: `profiler-cli thread markers --category Test --search <test> --list --limit 0`
- Question: the order and spacing of the test's log lines (navigation at +0 ms, the
  "Connection closed" console error at +123 ms, page loaded at +255 ms...). Everything in one
  test happens inside the same second.
- Got: `t=1m37s` for every one of ~30 markers, so the list gives order but no spacing.
- Workaround: `--json` and a script printing `.flatMarkers[].start`.
- Could have shown: millisecond timestamps (e.g. `t=97060.2ms` or `1m37.060s`) in `--list`, at
  least when neighbouring rows share the same second.
