## `thread markers --list` rounds times to the second past one minute

- Command: `profiler-cli thread markers --session <s> --search "Killing background page,Failed to launch,will retry" --list --limit 0`
- Expected: millisecond times, to order events that happen within the same second (a child crash, then the first failed process launch 80 ms later).
- Got: `t=2m12s` for all of them.
- Workaround: `--json` and a script printing `start/1000`.

## Question: which tests were running at time T?

- Question: "at the moment launches started failing (first `Failed to launch` marker), which `test` interval markers were in progress?"
- Command: `profiler-cli thread markers --search "name:test,will retry,Failed to launch" --list --limit 0 --json | python3 breakdown.py` (a script filtering interval markers whose [start, end] contains T).
- What could have shown it: `thread markers --at <t>` (or `--overlapping <t>`) listing the interval markers spanning a timestamp or a marker handle.
