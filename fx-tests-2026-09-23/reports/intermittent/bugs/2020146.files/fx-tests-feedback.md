# fx-tests feedback (bug 2020146)

## Question: what actually killed the job, for a `[taskcluster:error] Aborting task...` bug?

- Command: `fx-tests intermittent --bug 2020146 --since 30 --limit 0`
- The bug's summary names max run time, but 58 of its 167 jobs were killed by generic-worker's memory guard (`task aborted due to sustained memory usage above 90% and available memory less than 500.00 MiB`). Nothing in the output says so: "Failure messages" lists only test failure lines (3 of 167 jobs have one).
- Workaround: downloaded all 167 logs (768 MB) and grepped the final `[taskcluster:error]` line.
- What would have answered it: the task's final `[taskcluster:error]` line (or Taskcluster's run `reasonResolved`) per occurrence, grouped like "Job names".

## Question: were these jobs single-chunk?

- "Job names, chunk numbers merged" merged `mochitest-plain` (a 1-chunk job running all 6802 tests) with `mochitest-plain-N`. The unsuffixed name was the discriminator for 92 of the 167 jobs.
- What would have helped: keeping unsuffixed job names apart from chunked ones in that table.
