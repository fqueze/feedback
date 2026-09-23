## Question: were these failures one retrigger/backfill batch, and when within the day?

- Command: `fx-tests test browser/components/translations/tests/browser/browser_translations_select_ai_feature.js --task-ids --limit 0` (and `--history --config linux2204-64-wayland/opt`)
- Expected: for each failing task, its time (not just the day) and its Treeherder job symbol / whether it was a backfill or retrigger; and in `--history`, a hint that a day's run count (223 vs ~15 usual) is inflated by backfills.
- Got: task IDs grouped by day only. 15 of the 16 failures turned out to be one backfill batch (`tr8ns-b606af2b1dd-bk`) completing within 40 minutes, which is the key fact for an environment diagnosis.
- Workaround: `curl https://treeherder.mozilla.org/api/jobs/?task_id=<id>` per task, plus the Taskcluster queue status for worker IDs/zones.
