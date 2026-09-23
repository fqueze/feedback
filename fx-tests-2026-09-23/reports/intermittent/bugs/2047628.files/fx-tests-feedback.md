# fx-tests feedback (bug 2047628)

## `intermittent --bug` misses TEST-UNEXPECTED-PASS/ERROR/NOTRUN lines

- Command: `fx-tests intermittent --bug 2047628`
- Expected: under "Tests named, per annotated job", the tests with unexpected results in each annotated job. Treeherder's `text_log_errors` for these jobs has `TEST-UNEXPECTED-PASS | /webaudio/.../mediastreamaudiosourcenode-routing.html` (3 jobs), `TEST-UNEXPECTED-ERROR | /websockets/basic-auth.any.sharedworker.html?wpt_flags=h2` and `TEST-UNEXPECTED-NOTRUN | /websockets/binaryType-wrong-value.any.html?wpt_flags=h2`.
- Got: `(none: no occurrence carried a TEST-UNEXPECTED-FAIL line naming a test ...)`.
- Why it matters: the other tests are what made these jobs orange, and 4 of the 5 jobs were starred only on this bug. Listing those lines would show at once that the bug's own line never fails a job.
- Workaround: the Treeherder `jobs/<id>/text_log_errors/` and `bug-job-map/?job_id=` APIs.

## `task` counts expected statuses as failing on wpt

- Command: `fx-tests task bXb4xO0LSp6YjsszOzWrqQ --profiles` (wpt-backlog job)
- Expected: the one unexpected result the log reports (`Got 1 unexpected results, with 1 unexpected passes`).
- Got: `354 failing` (218 TIMEOUT, 136 ERROR), all of them expected per metadata. The one TEST-UNEXPECTED-PASS is not singled out.
- Workaround: grep the log for `TEST-UNEXPECTED`.

## Question: "does this log line also show up in green runs of the same job?"

- Command tried: `fx-tests test testing/web-platform/tests/websockets/send-many-64K-messages-with-backpressure.any.js` → `No test path in the xpcshell and mochitest 21-day data`. No wpt coverage, and `errors` is mochitest/xpcshell only.
- Workaround: page through the Treeherder `jobs/?push_id=` API for the same job label on other pushes, download the logs, and grep them.
- What would have answered it: an `errors --message` or `intermittent --bug` view listing the jobs, green ones included, whose log contains the bug's line.
