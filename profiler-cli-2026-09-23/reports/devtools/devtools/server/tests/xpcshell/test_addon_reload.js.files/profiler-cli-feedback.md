## Question: "when did the TIMEOUT tests of this job start, relative to an event?"

- Command: `profiler-cli thread markers --session test_addon_reload.js-1 --search "name:test" --list --limit 0 --json`, then a Python script counting `test` markers by status and bucketing TIMEOUT start times.
- Expected: a grouped view of `test` markers by their `Status` field with start-time range per group (e.g. `--group-by field:status` showing first/last start).
- Got: `--list` gives 1742 rows; the aggregate view gives counts but not the start-time spread per status.
- Workaround: script over `--json`.
