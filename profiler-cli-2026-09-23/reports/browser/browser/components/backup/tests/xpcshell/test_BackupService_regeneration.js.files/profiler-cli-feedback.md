## Question: "what was the machine's CPU use over this time range?" (resource-usage profile)

- Command: `profiler-cli thread markers --session <ru> --search 'name:CPU Use' --list --limit 0` after `zoom push 638,647`
- Expected: each row showing the marker's main value (CPU Percent), or `counter list` exposing the CPU track.
- Got: rows with only start and duration; `counter list` says "No counters in this profile" and `profile info` says "No significant activity" although every CPU Use marker in the window reads 93-100%.
- Workaround: `--json` and a Python script reading `fields[].cpuPercent`.

## Multi-line marker text breaks the one-row-per-marker `--list` output

- Command: `profiler-cli thread markers --session <s> --category Test --search <test file> --list --limit 0`
- Expected: one row per marker.
- Got: CONSOLE_MESSAGE INFO markers with embedded JS stacks print 15 lines each, twice (the quoted duplicate), so the test's log became 60 KB and was truncated by the harness.
- Workaround: narrower `--search 'TEST-,Starting'` and `cut -c1-250`.

## (review) `-name:task` silently drops Runnable markers whose task name contains "Task"

- Command: `profiler-cli thread markers --session <s> --search '-name:TaskController::AddTask,-name:AsyncShutdown blocker,-name:task' --list --limit 0` after `zoom push 37.36,39.2`
- Expected: to exclude only the long-lived `task` interval marker (the xpcshell add_task marker).
- Got: `-name:` is a case-insensitive substring match on the Runnable payload's `name` field too, so `callback[DeferredTask.sys.mjs]:JS` disappeared, and I first concluded that no DeferredTask callback ran in that window.
- Workaround: a positive `--search DeferredTask` in the same range. An exact-match form (`name=task`), or a note in the output that N markers were excluded by each term, would have caught it.

## (review) Question: "how are these N markers distributed over time?"

- Command: `profiler-cli thread markers --session <s> --search DummyEvent --list --limit 0 --json` plus a Python histogram, to see whether a 2,900-marker DummyEvent storm was unique to the failure window.
- Default output: the aggregate gives the count and the average interval, but not when the markers happen. The storms turned out to happen at 6.7–11.3 s, 15.6–16.4 s and 18.2–18.6 s.
- Could have shown: a time sparkline or bucketed counts per marker name in the aggregate view, as `counter info` already does ("over time").
