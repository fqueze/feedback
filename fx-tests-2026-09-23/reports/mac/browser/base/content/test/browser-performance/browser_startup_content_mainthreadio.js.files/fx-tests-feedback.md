# fx-tests feedback (browser_startup_content_mainthreadio.js)

## Question: "the profile this failing test uploaded itself"
- Command: `fx-tests task EQVWoyWOTDGFdURr4tX_WA.0 --profiles` (and `XYQSBwHOSIGhIWL4w0MMog.0`)
- Expected: a profile URL for browser_startup_content_mainthreadio.js. Its failure message names one:
  "open the profile_startup_content_mainthreadio.json artifact in the Firefox Profiler".
- Got: no profile line for that test (the harness does not upload a per-test profile when the
  manifest sets MOZ_PROFILER_STARTUP), so the test looked profile-less.
- Workaround: listed the task's artifacts with
  `curl .../api/queue/v1/task/<id>/runs/0/artifacts` and found
  `public/test_info/profile_startup_content_mainthreadio.json`.
- What could have shown it: any `public/test_info/profile_*.json` artifact that is not a
  harness per-test profile or the resource-usage one, listed under the job (or under the test
  whose message names the file). The same job also had an unexplained `profile_0_6094.json`.

## Question: "the first failing push and the last passing push on one config"
- Command: `fx-tests test <path> --history --config windows11-64-24h2-artifact`
- Expected: enough to name the regressing push for a config that went from 0% to 100%.
- Got: per-day counts only (2026-09-10: 4 pass, 6 fail). `--task-ids` gives only failing tasks.
- Workaround: task definitions of the failing tasks via the Taskcluster API for their revisions,
  then `hg json-pushes` for the pushes around them, then a Python loop over the previous push's
  task group to find a passing task and read its log.
- What could have shown it: a per-push (revision, pass/fail) listing for one config, or the
  revision next to each task id in `--task-ids`, plus the last passing task before the first
  failure.
