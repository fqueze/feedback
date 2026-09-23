## `fx-tests test` has no geckoview-junit data

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/ExtensionActionTest.kt`
- Expected: rates per config and failure modes for a GeckoView junit test (bug 1607766 is starred 12x/week on `geckoview-junit-fis`).
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..." (exit 2).
- Workaround: `fx-tests intermittent --bug 1607766` for the failing tasks, then the Treeherder `/api/project/<repo>/jobs/?job_type_name=...` endpoint to count the runs (denominator) per config.
- Question it could answer: "how many geckoview-junit jobs ran per config, and how many failed this test".

## `fx-tests task` says the resource-usage profile has no tests, but it has 1457

- Command: `fx-tests task L3gEGjcGR4COuZ0ppbIJwA --profiles`
- Expected: the test outcomes from the job's resource-usage profile.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus "128 failing markers named no test path". The same profile, loaded in profiler-cli, has 1457 `test` markers with Status PASS/FAIL/SKIP (e.g. `FAIL — org.mozilla.geckoview.test.ExtensionActionTest#disableTest[#pageAction]`, 30.764s).
- Likely cause: junit test names (`org.mozilla...Class#method[param]`) are not paths. Also, AssumptionViolatedException results are reported as FAIL there (`setBadgeText[#pageAction]`), which is what the "128 failing markers" count.
