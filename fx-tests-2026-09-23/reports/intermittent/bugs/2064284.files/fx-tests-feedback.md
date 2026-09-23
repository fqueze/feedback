## Question: which distinct failures are sheriffs starring on this (non-test) bug?

- Command: `fx-tests intermittent --bug 2064284 --since 40 --limit 0 --full-messages`
- Expected: per occurrence, the error line the sheriff starred (Treeherder's classified failure line / bug suggestion), so a catch-all bug can be split into its failure modes.
- Got: job name and task id only; "Tests named, per annotated job: (none ... the API only keeps lines matching TEST-UNEXPECTED-FAIL)". For toolchain / [taskcluster:error] bugs that is always empty.
- Workaround: downloaded all 55 `live_backing.log`s and regex-classified them (`classify.py`); one of them (a misstarred wpt job) was 305 MB.
- Could have shown: the first `error:`/`FAILED:` line, or Treeherder's text_log_errors for the job, per occurrence.
