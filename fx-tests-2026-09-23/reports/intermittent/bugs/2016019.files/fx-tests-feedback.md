## Question: "failure rate and history of a wpt job / wpt test on one config"

- Command: `fx-tests test testing/web-platform/tests/svg/animations/end-of-time-001-crash.html`
- Expected: rates per config, `--history`.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...` (wpt is not covered).
- Workaround: Treeherder REST `jobs/?job_type_name=test-windows11-64-25h2/debug-web-platform-tests-crashtest`
  (+ `-swr`, both repos), then `jobs/<id>/text_log_errors/` and `bug-job-map/?job_id=` per failed job to
  classify them. Note `start_timestamp__gte` was ignored by that endpoint; filtered client-side.
- What would have answered it: `fx-tests job <job type name> --history` (pass/fail per week for a job
  type, with the failed task IDs), even without per-test wpt data.

## Question: "which failure modes are behind this catch-all bug?"

- Command: `fx-tests intermittent --bug 2016019`
- Got: job names, platforms and task IDs (useful), but only 1 of 6 rows had a "Failure message".
- Needed: the last ERROR lines of each annotated job (Treeherder `text_log_errors`), which is what
  separates the modes of a harness-level bug like "No suite end message was emitted by this harness".
