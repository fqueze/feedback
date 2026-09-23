## Question: "when did this failure mode stop, and on which revision/image did it last fail?" (browser_utility_audioDecodeCrash.js)

- Command: `fx-tests test ipc/glue/test/browser/browser_utility_audioDecodeCrash.js --task-ids --limit 0` (and `--json`)
- Expected: for each failing task, its repo + revision (and ideally push time and worker pool/image), so the last failing push and the stop window can be read directly.
- Got: task id, job name and run *day* only. I had to run `fx-tests task <id>` once per task to get the revision, then hg `json-pushes` for push times, then Taskcluster `task/<id>` and `live_backing.log` (grepping `sboms/...`) for the worker pool and image. ~100 extra requests; hg.mozilla.org then started returning 406.
- Trap worth documenting: `--history` / `--task-ids` bucket by the day the task *ran*, not the push date. Here the only 2026-09-11 "failure" was a 2026-09-11 run of a 2026-09-06 central revision on a new `win11-64-25h2-alpha` pool, which made the mode look one day longer-lived than it was on current code.
- Also missing: passing task ids per config/day. To tell a code fix from a worker-image change I needed the image of *passing* runs after the stop, and had to use the Treeherder jobs API.
