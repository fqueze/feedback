## Question: "when did the job's tests stop passing, and how many TIMEOUTs share the same ~45 s duration?"

- Command: `profiler-cli thread markers --search "name:test" --list --limit 0 --json --session ...` on a resource-usage profile (FADMjPT5QXm-7tdUJVLRRQ), then a Python script grouping by status prefix of `label`, start-time histogram per second, last PASS start, and duration clustering.
- Expected: a default text view able to answer "status counts of `test` markers, and the start-time range / duration distribution per status" (e.g. `--group-by field:status` with min/max start and duration stats per group).
- Got: `--list` gives 3,700+ rows (unreadable); the aggregated default view groups by marker name only, so all statuses are merged under `test`.
- Workaround: `--json` + script.
