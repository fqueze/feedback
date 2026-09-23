## Question: the distinct failing tasks of one test on a try push, with their configs

- Command: `fx-tests try 9db28f6db8bf1b77ad7ed9de11211a54450dfc61 --profiles --task-ids`
- Expected: the 8 failing tasks, one line each with its config.
- Got: `task <id>` lines repeated once per failing execution (each task twice, first run and retry), cut after 5 with `… 11 more tasks`, and the profiles cut after 5 with `… 3 more profiles`; `--test <path>` gives the per-config table but no task IDs.
- Workaround: `--task-ids --json` and dedupe `taskIds[]` by `taskId`. The text output could list each task once with its execution count, or `--test <path> --task-ids` could add the task IDs to the per-config table.
