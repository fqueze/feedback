## `thread markers --list` times are rounded to the second past 1 minute

- Command: `profiler-cli thread markers --session cssgrid_01-1 --category Test --search cssgrid_01 --list --limit 0`
- Expected: start times precise enough to order and subtract (ms), as `t=12.671s` is for early markers.
- Got: `t=1m1s` for every marker between 61.0 and 61.99 s; the TEST-PASS, INFO and failure lines all read `t=1m1s` / `t=1m6s`, so neither the order nor the gaps are readable.
- Workaround: rerun every list with `--json` and print `start` from a Python one-liner. Needed for nearly every list in this investigation (test log, RefreshObserver, Composite, CompositeToTarget).

## Question: "machine CPU % over a time range" in a resource-usage profile

- Command: `profiler-cli thread markers --session cssgrid_01-ru --search "name:CPU Use" --list --limit 0 --json`, then `profiler-cli marker info <200 handles> --json`, then a script.
- The `--list` output (text or JSON) has no payload fields, so the CPU percentage of each `CPU Use` marker is not there; `marker info` on hundreds of handles plus a script was the only way to see "was the machine busy between 633 and 643 s".
- What could have shown it: `--list` printing the marker's schema fields (CPU Percent, Read/Written for IO) on the row, or a `counter`-like summary of `CPU Use` markers over the zoom range.
