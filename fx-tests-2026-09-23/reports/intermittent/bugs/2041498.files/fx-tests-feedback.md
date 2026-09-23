# fx-tests feedback (bug 2041498, jit-test on windows11-64-25h2-ccov)

## Question: how often does this job fail, and on which tests? (jit-test harness)

- Command: `fx-tests test js/src/jit-test/tests/wasm/memory64/basic.js` (also tried a victim, `js/src/jit-test/tests/self-hosting/object-lookup-hazard.js`)
- Expected: per-config rates for a jit-test, or a clear "jit-test is not covered".
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (exit 2). Only the wording hints that jit-tests are out of scope.
- Workaround: Treeherder REST API directly:
  `https://treeherder.mozilla.org/api/project/mozilla-central/jobs/?job_type_name=test-windows11-64-25h2-ccov/opt-jittest-1proc-6&count=2000&last_modified__gt=<date>`
  gave 166 of 178 completed jobs `testfailed` in a month, which `fx-tests intermittent --bug` (2 annotations in 7 days) cannot show. It would help to say explicitly that the jittest harness is unsupported, and point at the job-level view.

## Question: what artifacts does this task have, when it has no profile?

- Command: `fx-tests task Xb7w71mJQoGVqsjF2kvzHQ --profiles`
- Expected: the job's failures from its log, or at least the list of artifacts that do exist.
- Got: "task ... has no profile_resource-usage.json: the artifact is not there. Taskcluster expires task artifacts after about a month, so this is permanent" (exit). The task is one day old: the artifact never existed (jittest does not upload one), so the "expired" explanation is misleading.
- Workaround: listed artifacts with the Taskcluster queue API and parsed `live_backing.log` by hand.

## Question: which bugs are the other failing jobs of this job type starred on?

- `fx-tests intermittent --bug 2041498` gives this bug's annotations, but not that the same job is starred on bug 1921585 and on Treeherder internal issues too. Found with `/api/project/mozilla-central/bug-job-map/?job_id=<id>` per job.
