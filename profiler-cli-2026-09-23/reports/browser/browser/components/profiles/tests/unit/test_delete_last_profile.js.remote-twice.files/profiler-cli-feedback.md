## `--search name:task` matches every marker whose name contains "task"

- Command: `profiler-cli thread markers --session <s> --search "DeferredTask,pds-datastore-changed,...,name:task" --list --limit 0`
- Expected: `name:task` to match the xpcshell `task` markers (the add_setup / add_task intervals), i.e. an exact name.
- Got: 11,946 markers (1.4 MB of output), because `name:` is a substring match and hits `TaskController::AddTask`, `TaskQueue::DispatchLocked`, etc.
- Workaround: dropped the term; found the task markers through `--category Test`.
- Suggestion: an exact-match form (`name:=task` or `name:^task$`), or a warning when one term of a comma list matches thousands of markers.
