## Question: which tests ran in this browser session, with their status?

- Command: `profiler-cli thread markers --session S --category Test --search name:test --list --limit 0`
- Expected: the `test` interval markers only (one row per test: PASS/FAIL — path).
- Got: 520 rows; `name:` also matches the `name` payload key of every Text marker, so all TEST-PASS/INFO rows came back.
- Workaround: `--list --limit 0 --json | jq 'select(.name=="test" or .name=="TEST-UNEXPECTED-FAIL")'`.
- Could have shown: a name-only filter (e.g. `marker-name:test`), or a `--group-by name` view that lists the rows of one name.

## Question: from when to when was one refresh-driver tick reason present, and was it continuous?

- Command: `profiler-cli thread markers --session S --search RefreshDriverTick --group-by field:name` answers "how many", not "since when".
- Expected: for each distinct value, first and last timestamp and the longest gap (a timeline per value).
- Got: counts and durations only.
- Workaround: dumped all RefreshDriverTick markers with `--json` and bucketed per second / computed gaps with jq.
- Could have shown: `first`/`last` time per group in `--group-by` output.
