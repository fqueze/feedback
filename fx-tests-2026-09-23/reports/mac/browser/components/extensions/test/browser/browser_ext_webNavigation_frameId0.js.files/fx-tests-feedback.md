## Question: in which jobs did the leaker fail but this victim not, and what separated them?

- Commands: a loop of `fx-tests task <id> --passed --limit 0` and `--messages --limit 0` over the 17 leaker-failing tasks that are not in `fx-tests test <victim> --task-ids`, grepping each for the victim's outcome and for CRASH/TIMEOUT rows in between; then the job logs for `TEST-START | Shutdown` / `Main app process: exit`.
- Expected: something like `fx-tests test <victim> --after <leaker>` listing the jobs where both ran, each with the victim's outcome and whether the browser restarted between them (standalone, a timeout or a crash in between).
- Got: only per-job views; the restart after a timeout is not visible in `fx-tests task` output at all.
- Workaround: the loop above (`leaker-tasks-without-frameId0-failure-detail.txt`) plus job logs.
