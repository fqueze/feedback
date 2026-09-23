## Question: "the task IDs of every failing job of this one test on a try push"

- Commands: `fx-tests try <rev> --all-jobs --test <path> --task-ids` (per-config table, no task IDs printed despite `--task-ids`); `fx-tests try <rev> --profiles --task-ids --limit 0` (the row still ends with `… 13 more tasks` and `… 4 more profiles`: `--limit 0` does not expand the per-row lists).
- Expected: with `--test <path> --task-ids`, the task IDs (and retry IDs) behind each configuration's row; or `--limit 0` expanding the per-row task/profile lists.
- Got: the IDs only through `--json` (`permaFails[i].taskIds`).
- Workaround: `fx-tests try <rev> --json --limit 0` and a Python filter on the path.
