## `thread markers --list` timestamps are rounded to the second

- Command: `profiler-cli thread markers --category Test --search browser_searchSuggestions.js --list --limit 0 --session ...`
- Question: how many ms between the click, the page load, the failing assertion, and the async DB write finishing?
- Got: every row says `t=1m18s` / `t=1m19s`, so ordering and gaps within one subtest (tens of ms) cannot be read. `marker info` also prints `Time: 1m18s`.
- Workaround: `--list --json` and read `start` from `flatMarkers`. The text output could print ms (e.g. `t=78449.6ms`, or `1m18.450s`).

## `--list --json` flatMarkers have no `end` for interval markers

- Command: `profiler-cli thread markers --search formhistory.sqlite --list --limit 0 --json --session ...`
- Question: when did each `AsyncShutdown blocker ... Transaction (N)` interval end?
- Got: `start` only, no `end`/`duration` in the flat entries (the text list shows durations).
- Workaround: `marker info m-N --json` one by one for `end`/`duration`.

Correction to the entry above: flat entries do carry `duration` (not `end`); my script read the wrong key. Not a tool problem — disregard it.
