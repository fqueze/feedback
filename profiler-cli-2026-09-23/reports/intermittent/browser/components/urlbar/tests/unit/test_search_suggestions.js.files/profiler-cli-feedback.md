## Question: what was the machine's CPU use while one test ran? (resource-usage profile)

- Command: `profiler-cli zoom push m-1 --session S` (the test's `test` marker), then `profiler-cli thread markers --session S --search "name:CPU Use" --list --limit 0`
- Expected: a summary of the machine CPU over the zoomed range (mean / min / share at 100%), or at least the cpuPercent value on each row.
- Got: 372 rows of `CPU Use ... 100ms` with no value; `profile info` says "No significant activity" and `counter list` says "No counters in this profile", although the CPU Use markers carry cpuPercent/idle_pct.
- Workaround: `--list --limit 0 --json` piped to python to average `cpuPercent` per 2 s bucket and per minute.
- What would have answered it: `profile info` / `thread info` on a resource-usage profile summarising the CPU Use markers as a CPU-over-time track (like the "CPU activity over time" block), respecting zoom.

## `thread markers --search name:task --list --limit 0` hangs on a 1.45M-marker thread

- Command: `profiler-cli thread markers --session S --search "name:task" --list --limit 0` (full range, no --category) on a per-test xpcshell profile whose main thread has 1,454,811 markers (mostly `Runnable DummyEvent` + `TaskController::AddTask`).
- Expected: the ~25 `task` interval markers within seconds.
- Got: no output; the Bash call was killed at the 2-minute limit.
- Workaround: `zoom push` a narrow range first, or add `--category Test`.

## (review) Unzoomed sample queries count 13 of a thread's 1097 samples

- Command: `profiler-cli thread functions --session S --include-idle` (also `thread samples-top-down`, with or without `--include-idle`) on the AR9 per-test profile (`profile_test_search_suggestions.js.json`, main thread t-0), with `status` reporting "View range: Full profile" and no filters.
- Expected: totals over all 1097 samples that `thread info` reports.
- Got: `(root) ... total: 13 (100.0%)`. After `zoom push 0,30.4` (the whole profile) the same command gives `(root) total: 1097`.
- Workaround: `zoom push` the full range explicitly before any samples query.
