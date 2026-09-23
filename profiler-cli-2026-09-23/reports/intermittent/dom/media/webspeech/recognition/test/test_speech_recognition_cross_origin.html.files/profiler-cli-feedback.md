## `thread markers --list` times are rounded to the second once past 1 minute, even when zoomed in

- Question: "in what order, and how far apart, did start / audiostart / soundstart / result / end fire in this 15 s session?"
- Command: `profiler-cli zoom push 156,172 --session S; profiler-cli thread markers --search DOMEvent --list --limit 0 --session S`
- Expected: millisecond start times (a 16 s zoom holds hundreds of markers that are milliseconds apart).
- Got: every row reads `t=2m36s` or `t=2m51s`, so the list cannot give the order or the gaps.
- Workaround: `--json` and a script that prints `flatMarkers[].start`. The output could print `t=156.308s` (or `2m36.308s`) whenever the view is shorter than a few minutes.

## `thread list` ignores the zoom, so it cannot say which of 12 same-named threads served the session in view

- Question: "which Parakeet thread ran the recognition session between 156 s and 171 s?"
- Command: `profiler-cli thread list --session S` after `zoom push 156,172`
- Expected: CPU and marker counts restricted to the committed range, or a flag for that.
- Got: whole-profile totals. The utility process has 12 `Parakeet` threads, one per session, and the list does not say which one covers the range.
- Workaround: `profiler-cli profile markers --search Parakeet` and reading the thread column.
