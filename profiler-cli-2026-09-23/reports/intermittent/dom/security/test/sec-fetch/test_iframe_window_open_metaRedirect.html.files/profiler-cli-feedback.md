## Question: how far apart are two consecutive markers in a --list, past the first minute?

- Command: `profiler-cli thread markers --session <s> --search sec-fetch --list --limit 0` on a resource-usage profile (Android mochitest job, test at t≈5m13s).
- Expected: timestamps precise enough to order and space markers (the two `?pageC` checks were 107 ms apart, the double-finish 4 ms after).
- Got: `t=5m13s` / `t=5m14s` for every row — second resolution once t > 1 min, so ordering and gaps are invisible.
- Workaround: `--json` and print `flatMarkers[].start` in ms with a Python one-liner.
- Could have shown: millisecond precision (e.g. `t=313.396s`) in `--list` rows, at least when neighbouring rows share the same second.

## `--json` flatMarkers: interval markers have no end

- Command: same as above with `--json`; `m.get('end')` on `test` markers (776 ms duration in the text view).
- Expected: an `end` (or `duration`) field for interval markers.
- Got: no usable end in `flatMarkers[]` (start only in what I read); had to rely on the text view's duration.
