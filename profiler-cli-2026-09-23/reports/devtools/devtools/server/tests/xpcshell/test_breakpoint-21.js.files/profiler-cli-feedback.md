## "Which tests timed out together, and when did they start relative to the first launch failure"

- Command: `profiler-cli thread markers --session <s> --search "status:TIMEOUT" --list --limit 0 --json`, then a Python script (`timeouts.py` here) for counts before/after a time and start/duration ranges.
- The default `--list` output is 1060 rows; the aggregated (non-`--list`) view gives count and duration stats for `test` markers but not the start-time spread, nor a split at a given time.
- What would have answered: start-time min/max next to duration stats in the aggregated view, or a `--before/--after <t>` split.
