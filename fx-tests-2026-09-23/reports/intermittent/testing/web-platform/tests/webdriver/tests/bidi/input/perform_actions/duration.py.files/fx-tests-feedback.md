## `fx-tests task` says a wpt job's resource-usage profile "records no tests at all"

- Command: `fx-tests task LKCJat2bTpyQtnLxkRGC3A --profiles` (a web-platform-tests-wdspec job)
- Expected: the job's tests and their outcomes, or at least the failing subtest.
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one". The same kind of profile (co9NpsxFRD2SLWWwUEKlew) holds 80 `test` markers, 1206 `PASS` and 18 `FAIL` markers, including the failing subtest.
- Workaround: read `public/test_info/wpt_errorsummary.log` and the log directly, and load the resource-usage profile in profiler-cli.

## The tests behind a bug that spans two test paths, per job

- Question: which of the annotated jobs failed the bidi test and which the classic one (bug 2070526 is starred on both `/webdriver/tests/bidi/.../duration.py` and `/webdriver/tests/classic/.../duration.py`).
- Command: `fx-tests intermittent --bug 2070526`
- Got: a "Tests named, per annotated job" count (3x bidi, 3x classic) and a separate occurrence list without the test path, so the two cannot be joined; also only the last 7 days (the bug has 19 annotations since 2026-09-09 on Treeherder's failuresbybug).
- Workaround: fetched every task's `wpt_errorsummary.log` and the Treeherder `failuresbybug` API.
- Could show: the test path on each occurrence row, and a `--since` to reach back to the bug's filing.
