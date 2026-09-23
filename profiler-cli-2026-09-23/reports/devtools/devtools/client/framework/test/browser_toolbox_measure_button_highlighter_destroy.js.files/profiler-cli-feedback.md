## When did this marker start, to the millisecond?

- Command: `profiler-cli thread markers --session S --search RDP --list --limit 0` (zoomed to 250 ms)
- Expected: start times precise enough to order events a few ms apart.
- Got: every row says `t=4m59s`. The report I was checking orders a click and a response 1.25 ms apart, so this column could not confirm or refute anything. I had to use `--json` and a script over `flatMarkers[].start` for every list.
- What would have answered it: when the view is zoomed below a few seconds, print start times to ms (e.g. `t=298.909s`), or add a flag for that.
