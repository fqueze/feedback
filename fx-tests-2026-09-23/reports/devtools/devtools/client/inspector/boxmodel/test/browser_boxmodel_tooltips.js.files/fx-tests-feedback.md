## Question: the failing task IDs of one test on a try push, across all its configs

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --task-ids --profiles --messages --full-messages --limit 0`
- Expected: with `--limit 0`, every task ID behind the test's row (21 jobs here), one per job.
- Got: the row lists 5 task lines (each duplicated, one per failure message) then `… 37 more tasks`, and `… 16 more profiles`; `--limit 0` does not lift that per-row cap. Also `fx-tests try <rev> --test <path> --task-ids` prints only the per-config table, silently ignoring `--task-ids`.
- Workaround: `--json` and a script over `permaFails[].taskIds`, deduplicated by (taskId, retryId).
- What would have answered it: `--test <path> --task-ids` printing one task ID (and run) per failing job, grouped by config, like the per-config table it already prints.
