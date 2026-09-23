# fx-tests feedback (MediaDelegateXOriginTest.kt, bug 1990004)

## Question: "how often does this geckoview-junit test fail, and on which configs?"
- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/MediaDelegateXOriginTest.kt` (and `--history`).
- Expected: rates per config, or a clear "junit is not covered".
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..., It may have been renamed ... or never run in CI." The test runs in every geckoview-junit job; the message suggests a typo instead of "this harness is not indexed".
- Workaround: Treeherder `api/failuresbybug/?bug=1990004` for annotations, and sampling `api/jobs/?push_id=` plus log greps for a denominator.
- Could have shown: that geckoview-junit is out of scope, pointing at `intermittent --bug`.

## Question: "which tests ran and failed in this junit job?"
- Command: `fx-tests task FLIPpavAQHmAHmFRrDz-qQ --profiles`
- Expected: the job's test table, with the first failure.
- Got: "0 tests, 0 executions, 0 failing" / "This profile records no tests at all", plus a warning that 99 failing markers named no test path. The resource-usage profile has a `test` marker per junit test with its status (e.g. `FAIL — org.mozilla.geckoview.test.MediaDelegateXOriginTest#...`).
- Workaround: grep the live log for TEST-START/TEST-UNEXPECTED; `profiler-cli thread markers --search name:test`.
- Could have shown: junit tests keyed by class#method when there is no path.
