## The failing task IDs of one test on one config of a try push

- Command: `fx-tests try 2888bcab0070497cb5b516e6754d66679efb0a88 --profiles --task-ids --limit 0`
- Expected: every failing task ID for `browser_net_simple-request-data.js`, or a way to narrow to one config (e.g. the Windows ones).
- Got: 5 task IDs then `… 37 more tasks`, even with `--limit 0` (which applies to rows, not tasks). `--config` is refused on `try`.
- Workaround: `--json` and a Python script over `permaFails[].taskIds`. The text output could print the full task list per config under `--task-ids --limit 0`, or group tasks by config.
