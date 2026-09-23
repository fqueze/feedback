## `fx-tests test` has no WPT data

- Command: `fx-tests test testing/web-platform/tests/screen-capture/getdisplaymedia-framerate.https.html` (also tried without the `testing/web-platform/tests` prefix; `--harness` only accepts xpcshell|mochitest).
- Expected: per-config rates, history, and the failing task IDs, as for mochitest.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". Meanwhile `fx-tests intermittent --bug 1925691` and `fx-tests task <wpt task>` do work on WPT.
- Workaround: a script (scan2.py) that lists every macosx1500-aarch64 wpt chunk job through the Treeherder `jobs/?job_type_name=` API, keeps the tasks whose `MOZHARNESS_TEST_PATHS` contains the test's directory, and reads each `wpt_errorsummary.log`. It took about 4000 HTTP requests and ~5 minutes.

## Question with no answer: "does this failure follow the machine?"

- Needed: pass/fail per worker ID (Treeherder `machine_name`), for both failing and passing runs of one test. Here it was decisive: 36/36 runs on one machine subset failed, 0/155 elsewhere.
- `fx-tests intermittent --bug N` and `fx-tests test --task-ids` print task IDs but no worker IDs. Getting them took one Taskcluster `task/<id>/status` call per task, and the passing runs needed the scan above.
- What would have answered it: a `--by-worker` (or a worker column in `--task-ids`) on `fx-tests test`/`intermittent`, covering passing runs too.

## `fx-tests task` on a WPT job lists expected results as failures and misses the unexpected subtest failures

- Command: `fx-tests task ErYQo183S-SKqXAu9Wa0lA --profiles`
- Expected: `/screen-capture/getdisplaymedia-framerate.https.html` and the other screen-capture tests listed, since each had TEST-UNEXPECTED-FAIL subtests (the reason the job was orange).
- Got: "FAILED (18)", made of tests whose *expected* status is ERROR (e.g. `/screen-capture/capture-controller-event-target.https.window.html` logs `TEST-ERROR`, not `TEST-UNEXPECTED-ERROR`), and none of the tests with unexpected subtest FAILs.
- Workaround: read `wpt_errorsummary.log` and the live log directly.
