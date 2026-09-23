## Question: which tests ran in this per-test profile, with their statuses?

- Command: `profiler-cli thread markers --session <s> --category Test --search name:test --list --limit 0`
- Expected: the 14 `test` interval markers (one per test, `PASS — <path>` / `FAIL — <path>`).
- Got: 999 markers — `name:test` also matched TEST-PASS/INFO/TestUtils markers (substring match on name and on the `name` payload key), so the per-test rows were buried.
- Workaround: `--list --limit 0 --json` piped to a script keeping `m['name'] == 'test'`.
- What would have answered it: an exact-name filter (e.g. `name=test`), or a `--group-by name` list mode that prints the rows of one exact name.

## Question: machine CPU % over a time range of a resource-usage profile

- Command: `profiler-cli counter list` / `counter info c-0` on `profile_resource-usage.json`; then `thread markers --search "name:CPU Use" --list`.
- Expected: a CPU% series for the range (the resource-usage profile's whole point for "was the machine saturated?").
- Got: "No counters in this profile."; the marker list shows only `CPU Use  100ms`, with no percentage — each value needed a `marker info`.
- Workaround: `--list --json` and a script printing `data.cpuPercent` per marker.
- What would have answered it: show the `cpuPercent` field in the `--list` description column for `CPU Use` markers, or expose these markers as a counter.
