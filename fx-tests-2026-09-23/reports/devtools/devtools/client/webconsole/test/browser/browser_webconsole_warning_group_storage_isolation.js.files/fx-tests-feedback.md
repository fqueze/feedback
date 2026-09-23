## Question: the failing task IDs (and their profiles) of one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --messages --full-messages --limit 0`
- Expected: with `--limit 0`, every task ID and profile URL behind the test's row.
- Got: the row still ends in `… 37 more tasks` and `… 16 more profiles`. `--limit 0` applies to rows, not to the per-row task/profile lists. Task IDs are also listed once per execution, so each job appears twice.
- Workaround: `--json` and a Python script over `permaFails[].taskIds`, de-duplicating by taskId.
- What could have answered it: a per-row "all tasks" expansion (e.g. `--test <path> --task-ids` listing each unique job with its config and per-test profile URLs).
