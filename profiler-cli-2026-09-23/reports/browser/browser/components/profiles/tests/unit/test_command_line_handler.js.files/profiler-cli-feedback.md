## Question: which tests ran concurrently with this one (how loaded was the job)?

- Command: `profiler-cli thread markers --session <resource-usage> --search name:test --list --limit 0 --json`, then a Python script to keep the `test` markers overlapping the failing test's marker (54 of them).
- What the default output could have shown: for a zoom range (`zoom push m-1`), the markers overlapping it rather than only those starting in it, or a count of concurrent `test` intervals. This resource-usage profile (xpcshell, test-linux2404-64-artifact/debug) also had no CPU counters (`counter list` returned "No counters in this profile"), so concurrency was the only load signal.

## `--search name:task` matches every marker whose name contains "task"

- Command: `profiler-cli thread markers --session <s> --search "name:task" --list --limit 0` (to find the xpcshell `task` interval markers)
- Expected: only markers named `task`, or a way to ask for an exact name.
- Got: every `Perform microtasks`, `TaskController::AddTask` and `TaskQueue::DispatchLocked` marker too, hundreds of rows around the two `task` markers.
- Workaround: pipe through `rg -v`. An exact-match form (`name:=task` or similar) would avoid it.
