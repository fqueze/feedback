# fx-tests feedback (bug 2037297)

## `task --profiles` blames expiry for an artifact that was never uploaded

- Command: `fx-tests task U0IBdaLeQvi776t3_beClQ --profiles`
- Expected: "this job uploaded no profile_resource-usage.json" (the job aborted in mozharness'
  download-and-extract step, before any test ran), ideally with the job's first ERROR/FATAL line.
- Got: "the artifact is not there. Taskcluster expires task artifacts after about a month, so this
  is permanent". The task is 19 days old and its other artifacts (live_backing.log, ...) expire
  2027-09-03: nothing expired.
- Workaround: listed `.../runs/0/artifacts` by hand and read the log.
- Suggestion: check whether the log is still there before blaming expiry.

## Question: did a job-level (non-test) failure stop once its fix landed?

- Context: the failure is a mozharness traceback before any test runs (geckoview-test-verify-nofis
  aborting on `No module named 'yaml'`). `fx-tests test` needs a test path, and
  `fx-tests intermittent --bug 2037297 --since 30 --tree all` lists annotated failures only, not
  the passing runs of the same job.
- Needed: per-day pass/fail counts for a job name (`geckoview-test-verify-nofis`), to show
  "perma-fail 08-27..09-03, green from 09-04".
- Workaround: Treeherder REST `api/project/autoland/jobs/?job_type_name=...` plus a python script.
- What would answer it: `fx-tests intermittent --bug N` printing, for each annotated job name, the
  job's pass/fail history over the same window, or the date of the last annotation next to the
  bug's resolution date.
