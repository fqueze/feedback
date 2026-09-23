## `thread markers --list` times are rounded to the second past 1 minute

- Question: in which order did the CTA query, the observer notification, the two counter adds and the failure happen, within 1 ms of each other?
- Command: `profiler-cli thread markers --category Test --search searchCTA_connectivity --list --limit 0 --session ...`
- Expected: millisecond start times (e.g. `t=82.5527`), as markers a few ms apart are the whole story in a race.
- Got: `t=1m22s` / `t=1m23s` for every row, so the sequence could not be read.
- Workaround: `--json` and a script printing `start/1000` with 4 decimals. The output could show `t=1m22.553s`.

