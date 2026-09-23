## Question: how often does a python-test (source-test mozbase) test fail, per config?

- Command: `fx-tests test testing/mozbase/mozproxy/tests/test_proxy.py`
- Expected: rates per config, or at least the failing source-test jobs naming the file.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (exit 2).
- Workaround: Treeherder `api/project/<repo>/jobs/?job_type_name=source-test-python-mozbase-<platform>/opt`, then downloading and grepping 64 failing `live_backing.log`s for `TEST-UNEXPECTED-FAIL`. About 15 minutes of work.

## Question: are the jobs starred on this bug really the bug's test?

- Command: `fx-tests intermittent --bug 2057939`
- Expected: a warning that none of the annotated jobs' failure lines name the bug's test (`test_proxy.py::test_mitm`).
- Got: 5 annotations, all Android `geckoview-reftest` "incomplete after application is no longer top", shown without comment. A reader could take them for this test's rate. Treeherder `failuresbybug` shows 68 of the bug's 69 annotations are misstarred.
- Workaround: compared the "Tests named" section with the bug summary by hand.
