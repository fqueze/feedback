## Question: are these failures on current code, or on old revisions re-run that day?

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_resize_sidebar.js --task-ids --limit 0` (and `--json`).
- The output groups failing tasks by the day they ran, with no revision or push date. All 24 Mac 15.30 failures ran on 2026-09-14, but on autoland revisions pushed 2026-08-14/15 (retriggers), before the fix landed on 2026-08-28; the 38 `'NaN'` failures that ran on 2026-08-31 and 2026-09-02 are also on 2026-08-27/28 revisions. From the output alone they read as current failures, and "recent 7.9%" on Mac 15.30 looks like a live problem.
- Workaround: `curl https://firefox-ci-tc.services.mozilla.com/api/queue/v1/task/<id>` for each of 28 tasks (payload.env.GECKO_HEAD_REV), then `json-pushes?changeset=<rev>` for the push date.
- Could have shown: the revision (and its push date) next to each task ID, and a warning when a day's failures are on revisions much older than the day.

## Chunk numbers in `--task-ids` do not match the job names

- Command: `fx-tests test browser/components/sidebar/tests/browser/browser_resize_sidebar.js --task-ids --limit 0`.
- Expected: the chunk of each job, e.g. `test-macosx1500-aarch64/opt-mochitest-browser-chrome-7` for all 24 Mac 15.30 jobs (their Taskcluster `metadata.name`).
- Got: some listed as `...-chrome-7`, some as `...-chrome-1` (e.g. SqjE3rcdTyyg9fn2n0K1hg, whose Taskcluster name is `-7`), some with no chunk suffix (e.g. BMFOuIyvTPqzBQnP3eMo7g). The `--json` `chunk` field is the same: 1 for SqjE3rcdTyyg9fn2n0K1hg, `null` for BMFOuIyvTPqzBQnP3eMo7g and for the tsan job H0GhHrzSQVi95E9ZNovyHA.
- Workaround: read the name from the Taskcluster task definition.

## Question: did the annotated failure continue after the fix landed, and on which branches?

- Command: `fx-tests intermittent --bug 2063582 --since 30 --tree all` (the default, trunk and 7 days, says only "no sheriff annotations").
- The text output gives totals per message, job name and platform, but not per tree or per push date, so it cannot say that the 280 annotations are autoland/central on pre-fix pushes, then beta (156) until 2026-09-10 pushes and release (156.0.1) until 2026-09-15.
- Workaround: `--json`, then a script over `occurrenceRows` splitting on `tree`, `pushTime` against the fix's push time, and the failure line.
- Could have shown: a per-tree breakdown with the first and last push time, and with `--history`, per-day counts per tree.
