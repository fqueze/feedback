## GeckoView junit tests are invisible to `fx-tests test`
- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/TextInputDelegateTest.kt`
- Expected: rates per config, the failure modes, and `--history`.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". There are no run counts, so there is no failure rate.
- Workaround: `fx-tests intermittent --bug 1566689 --since 30 --limit 0` gave sheriff-starred counts and task IDs. For unstarred failures I reused a sibling report's Treeherder scan.
- Question it could have answered: "how many runs of this junit test, and how many failed, per config?"

## `fx-tests task` on a geckoview-junit job reports "0 tests"
- Command: `fx-tests task JY4-nP4aR0eEGXB3VKHuhg --profiles`
- Expected: the per-test table. The resource-usage profile has a `test` marker with a status for each of the 1457 tests.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus a warning about 134 failing markers that name no test path. Those are `assumeThat` skips, which the harness logs as FAIL.
- Workaround: `profiler-cli thread markers --search restartInput_temporaryBlur --list` on the resource-usage profile.
