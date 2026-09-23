## Question: the distinct failing tasks of one test on a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- Expected: `--limit 0` to list every task ID (and every profile URL) behind the test's row.
- Got: 5 task lines then `… 37 more tasks`, and 5 profiles then `… 16 more profiles`, even with `--limit 0`. Each task is also printed twice (once per failure of the pair), so the 5 shown cover only 3 tasks.
- Workaround: `--json`, then a script deduplicating `permaFails[].taskIds` by taskId.
- Could have shown: one line per distinct task, with its config and its profile URLs, uncapped under `--limit 0`.
