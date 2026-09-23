## Question: how busy was the machine during one test's run (resource-usage profile)?

- Command: `profiler-cli zoom push m-1` (the `test` marker) then `profiler-cli thread markers --search "name:CPU Use" --list --limit 0`
- Expected: a summary of the `CPU Use` markers' `cpuPercent` over the zoomed range (mean/median/share above 95%), or the value on each listed row.
- Got: 321 rows with only name/time/duration, no field values; aggregate view gives count and duration stats only. `counter list` says "No counters in this profile".
- Workaround: `--list --limit 0 --json` and a Python script averaging `data.cpuPercent`.
- What would have answered it: numeric field stats in the grouped view (e.g. `--group-by name` showing min/median/mean/max of numeric payload fields), or showing the key field in the `--list` row description.

## `--search "name:task"` matched thousands of TaskController markers

- Command: `profiler-cli thread markers --search "name:task" --list --limit 0` on a parent GeckoMain thread
- Expected: the harness `task` interval markers (21 of them).
- Got: 4603 markers (523 KB) — `name:` is a substring match, so `TaskController::AddTask` etc. matched.
- Workaround: `--category Test --min-duration 1`.
- Suggestion: an exact-match form (e.g. `name:=task` or quoting) for marker names.

## `thread samples` after `zoom clear` still reports the previously zoomed range (review-test_extensionsettings.js)

- Command: `profiler-cli zoom push 3.67,6.83`, `thread samples --include-idle`, `zoom clear`, `thread samples --include-idle` (session on the ST6PHGqa per-test profile, t-0)
- Expected: the second call to cover the full profile (2642 samples), as its header `View: Full profile` and `status` both say.
- Got: exactly the zoomed result again (264 samples, same categories and functions), under a `Full profile` header. Silent: nothing hints the numbers are stale.
- Workaround: `zoom push 0,<profile end>` for a whole-profile view, then `zoom pop`.
