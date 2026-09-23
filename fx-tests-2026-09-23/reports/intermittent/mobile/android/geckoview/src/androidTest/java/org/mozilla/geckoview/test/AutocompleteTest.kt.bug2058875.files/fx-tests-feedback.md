## geckoview-junit jobs are invisible to `test` and misread by `task`

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/AutocompleteTest.kt`
  - Expected: rates per config for the junit test (or a clear "junit is not covered" message).
  - Got: "No test path in the xpcshell and mochitest 21-day data contains ... It may have been renamed ..." — reads as a spelling problem rather than "this harness is not indexed".
  - Workaround: `fx-tests intermittent --bug 2058875 --since 60 --history --limit 0` for counts and task ids, then the raw `live_backing.log`.

- Command: `fx-tests task VJfKFZ0ATU6NNE1ayRY2Dg --profiles` (test-android-em-14-x86_64/debug-geckoview-junit-nofis)
  - Expected: the one TEST-UNEXPECTED-FAIL (`AutocompleteTest#creditCardSelectCoalescesAcrossSameTypeFields`) as the job's failure.
  - Got: "0 tests, 0 executions, 0 failing", "This profile records no tests at all. Either the job is not a test job, or the harness died", plus a warning of "129 failing markers in this job named no test path". The resource-usage profile does have one `test` marker per junit test (`FAIL — org.mozilla...Class#method`); most of the 129 are the junit harness's expected `TEST-FAIL` (assumption failures), not unexpected failures.
  - Question the default output could have answered: "which junit test failed unexpectedly in this job". Treating `Class#method` as the test name, and separating `TEST-FAIL` (expected) from `TEST-UNEXPECTED-FAIL`, would.
  - Workaround: `grep TEST-UNEXPECTED` in the downloaded log.
