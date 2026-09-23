## "How often does this geckoview-junit test fail, per config, relative to its runs?"

- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/DynamicToolbarTest.kt`
- Expected: rates per config and history for `DynamicToolbarTest#hitTestOnPositionSticky`.
- Got: `No test path in the xpcshell and mochitest 21-day data contains ...`. Nothing says that geckoview-junit is not indexed at all.
- Workaround: `fx-tests intermittent --bug 1921935` gave the 7-day task ids. For the numerator I used Treeherder `api/failuresbybug/`. For the denominator I paged `api/project/autoland/jobs/?job_type_name=<config>&last_modified__gt=...` per config and counted `success` + `testfailed`. That took about 6 calls and a script.
- What would have answered it: `fx-tests test` accepting a junit class or method id (`org.mozilla.geckoview.test.DynamicToolbarTest#method`) and giving per-config runs and failures. Failing that, one line saying "geckoview-junit is not covered".
