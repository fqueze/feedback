## Which failure modes are behind a bug's stars, when the starred jobs are not test jobs

- Question: bug 2065363 has 1,169 stars in 30 days, all on snap build jobs. Which failures are they, and when did each one start and stop?
- Command: `fx-tests intermittent --bug 2065363 --since 30 --history`
- Expected: a split by failure message, like "Failure messages, per annotated job" gives for test jobs.
- Got: that section listed only the 3 starred test jobs ("Tests named: none: no occurrence carried a TEST-UNEXPECTED-FAIL line"). Nothing broke down the 1,166 build jobs, so four different perma-failures looked like one.
- Workaround: took the jobIds from `--json` `occurrenceRows`, fetched `/api/project/<tree>/jobs/<id>/text_log_errors/` for all 1,169, and bucketed the lines with a script. That split exit 100 from exit 1, and newtab from patch failures. arm64 jobs still read only `[taskcluster:error] exit status 1`, so their logs had to be read to go further.
- What the output could show: a per-occurrence first error line from `text_log_errors`, or the grouped counts, when the bug's jobs carry no TEST-UNEXPECTED line. It could also show the bug each job type was starred on before and after, to spot sheriffs starring one bug for another.

## `fx-tests task` on a build job says the artifact expired

- Command: `fx-tests task RTIVm_YuS8aXPPIOd48Wgw --profiles` (snap build job, 2026-08-26)
- Expected: "not a test job, no resource profile".
- Got: "has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent…". The last sentence does say that non-test jobs never upload one. The task is under a month old, though, and its other artifacts are still there.
- Workaround: none needed. The tool could check the task's name or kind and lead with the non-test-job case.
