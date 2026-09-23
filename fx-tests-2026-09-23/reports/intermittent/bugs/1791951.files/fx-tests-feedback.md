## `fx-tests task` reports "0 tests" for web-platform-tests wdspec jobs whose resource profile has test markers

- Command: `fx-tests task CIaXD8eATM6V9vhX-NjSGg --profiles` (also `H42cz-VnSciY5W0kLlkKtg`, `KwgrFQKpTvWaTkZZOI5RDA`, `IKwUiuLATfuwdatcSO-TaQ`)
- Expected: the job's tests with their status (the resource-usage profile of CIaX has 127 `test` interval markers, e.g. `TIMEOUT — /webdriver/tests/bidi/browsing_context/activate/activate.py`).
- Got: `0 tests, 0 executions, 0 failing` and "This profile records no tests at all. Either the job is not a test job, or the harness died before it ran one", plus a warning listing the TIMEOUT markers as "failing markers in this job named no test path".
- Workaround: read `live_backing.log`, and `profiler-cli thread markers --search name:test --list` on the resource profile.

## No wpt coverage in `fx-tests test` / `fx-tests intermittent` drill-down for a bug whose failures are all wpt

- Command: `fx-tests test testing/web-platform/mozilla/tests/webdriver/classic/add_cookie/privileged.py`
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...".
- Question I could not answer with the tool: "for the jobs annotated on this bug, which test timed out and was the browser slow to start or to quit?" `fx-tests intermittent --bug 1791951` lists only `leakcheck` fallout lines under "Tests named" (42x) because the TEST-UNEXPECTED-TIMEOUT line is not in the parsed failure lines; the timed-out test path was only in the logs. I scripted over 102 `live_backing.log`s to get it. Showing the TEST-UNEXPECTED-TIMEOUT test path per occurrence would have answered it.
