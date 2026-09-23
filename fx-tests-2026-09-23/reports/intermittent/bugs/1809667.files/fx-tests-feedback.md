# fx-tests feedback (bug 1809667, max run time meta bug)

## Question: where did a job killed for max run time spend its wall time, and was the machine slow?

- Command: `fx-tests task Ngn3prflSSqauUhS5gs_XQ.0 --profiles` (same for every one of the 163 annotated jobs)
- Expected: the job's timeline — tests run and their durations, the phases (download-and-extract, create-virtualenv, run-tests), machine CPU / IO wait.
- Got: `task ... was killed for exceeding its maximum duration, so its profile is a partial stream rather than a finished document and this tool does not read that format.`
- Workaround: wrote three scripts (in this directory): `phases.py` parses the log's mozharness step timestamps and largest silent gap; `cpu.py` parses the partial `profile_resource-usage.json` stream (one JSON object per line) for the mean `iowait_pct`/`user_pct` of its `CPU` markers; `cmp.py` reads `*summary.jsonl` to compare per-test durations with passing runs of the same job.
- Cost: this is the only question a max-run-time failure asks, and every job of such a bug hits the refusal. The partial stream is valid line-by-line JSON and holds everything needed (Phase markers, CPU markers with iowait, test markers).
- What the output could have shown: per phase, its duration against the same job's passing runs; the mean CPU user and IO wait during tests; the last test running and how long it had been running; the largest gap without log output.

## Question: which mechanisms are behind a meta bug's annotated jobs?

- Command: `fx-tests intermittent --bug 1809667 --limit 0`
- Expected: some way to group 163 heterogeneous jobs by what killed them.
- Got: job names, platforms, and task ids — nothing about duration, the task's maxRunTime, or how far it got.
- Workaround: `agg.py` over every task's log (total, step durations, largest silent gap), then grouping by hand.
- What the output could have shown: per occurrence, its maxRunTime, the job's typical passing duration, and setup time; that alone separates "slow setup", "hung silently", "job always too long" and "tests uniformly slow".

## Question: how many jobs of a config hit their limit, annotated or not?

- The mac VM pool had 232 jobs at the 3600 s limit in 7 days, of which 31 were annotated on this bug. Only the Treeherder jobs API (`result=testfailed`, duration = maxRunTime) showed that; `fx-tests` has no view of unannotated or non-test-harness task timeouts.
