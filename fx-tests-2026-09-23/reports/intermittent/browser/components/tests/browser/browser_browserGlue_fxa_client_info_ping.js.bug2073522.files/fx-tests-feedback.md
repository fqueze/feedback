## Question: the minidump of the hung parent process behind this TIMEOUT

- Command: `fx-tests test <path> --task-ids --issue 2` (TIMEOUT row), then `fx-tests task <taskId>`.
- Expected: `--help` says `--task-ids` prints "the minidump IDs of any crashes"; the harness wrote a dump of every process when it killed the hung browser ("CRASH [Unknown]" is listed for the test in `fx-tests task`).
- Got: no minidump IDs anywhere for these timeout-kills, so `fx-tests crash <task> <id> --all-threads` (which works fine and was the decisive evidence) could not be reached from fx-tests output.
- Workaround: read the dump ID from the bug's log excerpt ("mozcrash Saved ...\876a222e-....dmp"), or from the resource-usage profile's "Writing a dump to ..." INFO marker via `profiler-cli marker info` (the list view truncates it), then match the parent PID by hand.
- What would have answered it: list the parent-process dump ID (and ideally the child ones) for timeout-kills in `fx-tests task` / `--task-ids`, the same as for crashes.
