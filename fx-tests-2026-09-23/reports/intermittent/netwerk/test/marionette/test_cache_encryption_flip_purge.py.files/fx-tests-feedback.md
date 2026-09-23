## Marionette tests: no failure rate, and `task` reports "0 tests"

- Command: `fx-tests test netwerk/test/marionette/test_cache_encryption_flip_purge.py`
  - Expected: rates per config, like for mochitest and xpcshell tests.
  - Got: "No test path in the xpcshell and mochitest 21-day data contains ..." Marionette is not covered.
  - Workaround: `fx-tests intermittent --bug 2048205 --since 30` for the failure counts. For the run counts, the Treeherder REST API (`/api/project/<repo>/jobs/?job_type_name=<job>&last_modified__gt=<date>`), one query per job type and repo.
  - Question it could not answer: "how often does this marionette test fail, per config". A `test --harness marionette`, or a run count next to the annotation count in `intermittent --bug`, would answer it.

- Command: `fx-tests task I5SR0tYeTY2oZX6JMvgggQ --profiles`
  - Expected: the job's test outcomes.
  - Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". But the job ran hundreds of marionette tests, and one failed. The message points to the wrong cause: the marionette resource-usage profile has no test markers.
  - Workaround: downloaded `live_backing.log` and grepped it.
  - Suggestion: say "this harness records no per-test markers in its resource profile" when the job is a marionette job.
