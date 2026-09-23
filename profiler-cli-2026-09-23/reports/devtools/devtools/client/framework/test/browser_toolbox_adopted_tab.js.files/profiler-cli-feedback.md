## Question: which tests ran before mine in this browser session?

- Command: `profiler-cli thread markers --session <s> --category Test --search "Entering test" --list --limit 0`
- Expected: each row to show the marker's `Test Name` field (the test file), since the per-test profile covers every test of the manifest before the failing one.
- Got: only the message ("Entering test " — empty for tests using unnamed add_task), so 30 rows of identical "Entering test" with no way to tell which test each belongs to.
- Workaround: `--json` and a Python script printing `data.test` of the first marker per test.

## Question: which GC majors were shrinking GCs, and for what reason?

- Command: `profiler-cli thread markers --session <s> --thread t-64 --search GCMajor --list --limit 0`, then `marker info m-339`
- Expected: the list rows (or at least `marker info`) to show `reason` and `options` (e.g. `USER_INACTIVE`, `Shrink`) — the two fields that tell a shrinking GC from a normal one.
- Got: list rows show no payload at all for GCMajor; `marker info` shows `timings: {"status":"completed","max_pause":...,"reason":"USER_INACTIVE","options":"Shrink",...` cut with an ellipsis, so it only worked because reason/options happened to be early in the blob.
- Workaround: `--list --json` and a script reading `data.timings.reason` / `.options` for every GCMajor.

## Minor: `marker info --json` has no `handle` field

- Command: `profiler-cli marker info m-72 m-339 --session <s> --json`
- Expected: each record to carry its `handle`, so several records can be told apart.
- Got: `handle` absent (None), only name/start/end.
