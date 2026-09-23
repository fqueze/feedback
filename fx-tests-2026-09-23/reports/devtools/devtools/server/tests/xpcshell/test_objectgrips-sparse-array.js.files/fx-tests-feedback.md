## Question: how did my test end in this job (a job with 488 failures)?

- Command: `fx-tests task LKys8j8iTQKTAN9hKv040g | grep objectgrips-sparse` (and the same for the 5 other tasks `fx-tests test <path> --task-ids` gave for this test).
- Expected: the row of the test whose `--task-ids` pointed me at this job.
- Got: nothing for all six tasks: the default list is truncated, and grep removed the truncation line. Needed `--limit 0` (1,969 lines) and a grep.
- Workaround: `fx-tests task <id> --limit 0 > file; grep`.
- What would have answered it: a `--test <path>` filter on `fx-tests task`, or having `fx-tests test <path> --task-ids` print the test's status and first message in each listed task.
