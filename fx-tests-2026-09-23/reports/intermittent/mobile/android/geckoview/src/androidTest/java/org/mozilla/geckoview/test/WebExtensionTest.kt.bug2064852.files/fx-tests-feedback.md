# fx-tests feedback (WebExtensionTest.kt.bug2064852)

## Question: which test failed unexpectedly in this geckoview-junit job?

- Command: `fx-tests task AvMqSvVIT5q20RlCKmIypw --profiles`
- Expected: the one TEST-UNEXPECTED-FAIL (`org.mozilla.geckoview.test.WebExtensionTest#testBrowserTabsRemove`), and the 1457 tests the job ran.
- Got: "0 tests, 0 executions, 0 failing" and "This profile records no tests at all", plus a warning that "128 failing markers in this job named no test path" listing `AccessibilityTest#testClipboard` etc. Those are `TEST-FAIL` lines (expected failures in the junit harness, not unexpected ones). The resource-usage profile does hold one `test` marker per junit test (1457 of them, `PASS`/`FAIL` + `pkg.Class#method`), including `FAIL — org.mozilla.geckoview.test.WebExtensionTest#testBrowserTabsRemove`.
- Workaround: grep `TEST-UNEXPECTED` in live_backing.log; `profiler-cli thread markers --search testBrowserTabsRemove --list` on the resource-usage profile.
- What would have answered it: treat `org.mozilla...Class#method` as a test name for geckoview-junit jobs, and tell expected `TEST-FAIL` from `TEST-UNEXPECTED-FAIL`.

## Question: failure rate of a geckoview-junit test

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/WebExtensionTest.kt`
- Got: no data (only xpcshell and mochitest are indexed). Expected given the harness, but there was no way to get a denominator; `fx-tests intermittent --bug` gave only the annotated numerator.
- Workaround: counted annotations with Treeherder `failuresbybug`; no run count.
