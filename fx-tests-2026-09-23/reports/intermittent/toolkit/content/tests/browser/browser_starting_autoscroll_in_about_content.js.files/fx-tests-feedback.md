## Question: in each failing job, which failure message did each run (first run, retry) produce?

- Command: `fx-tests test toolkit/content/tests/browser/browser_starting_autoscroll_in_about_content.js --executions` (and `--task-ids --limit 0`)
- Expected: per job, the combination of messages seen (here: path-finder `leaked window until shutdown` alone vs. with leaks.py's `leaked 1 window(s) until shutdown`), which tells which runs still had the window rooted versus already garbage.
- Got: `--executions` only says "284 of 372 failing jobs saw the failure more than once"; `--task-ids` prints one row per (task, message) with duplicated task ids and no grouping, so the per-job pattern is not readable.
- Workaround: `--task-ids --limit 0 --json`, then a Python script grouping `taskIds[]` by `(taskId, retryId)` and counting message combinations (275 both / 88 path-finder only / 9 path-finder x2).
- What the output could have shown: under `--executions`, a small table of per-job message combinations with job counts.
