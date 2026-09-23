# fx-tests feedback (bug 2071660)

## `intermittent --bug` truncates the job name, which was the discriminator
- Command: `fx-tests intermittent --bug 2071660`
- Expected: the Occurrences table to tell the three job types apart (cold-view-nav-start, newssite-applink-startup, tab-restore-startup), since the bug mixes two failure modes across them.
- Got: `perftest-an…` for every row; needed `COLUMNS=250`.
- Workaround: `COLUMNS=250 fx-tests intermittent --bug 2071660 --limit 0`.

## Question: "which devices / revisions did the annotated jobs run on?"
- Command: `fx-tests intermittent --bug 2071660 --json` + a script over `occurrenceRows[].machineName` / `.revision`.
- The default table has no machine or revision column. For Android hardware jobs the machine name (s24-01, s24-02...) is what shows a device-pool pattern; a `--wide` or machine column would have answered it.

## Question: "what is this job type's pass/fail history?"
- Nothing in fx-tests answers it for non-mochitest/xpcshell jobs (perftest): `fx-tests test testing/performance/mobile-startup/cvns.sh` says no data. Used the Treeherder `jobs/?job_type_name=` API instead. A job-name mode for `fx-tests test` (or `intermittent --bug` showing the job type's total runs next to its annotated failures) would answer "how perma is it, and on which device model".

## `task --profiles` message on a perftest job
- Command: `fx-tests task JUV3DWbsRfm7AdpX4wMw5Q --profiles`
- Got: "the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent" for a task that ran today. The next sentence ("A job that is not a test job never uploads one") is the actual reason; leading with expiry is misleading when the task is recent.
