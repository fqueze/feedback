# fx-tests feedback (MediaSessionTest.kt, bug 2051635)

## geckoview-junit tests have no per-test data
- Command: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/MediaSessionTest.kt`
- Expected: rates per config for a junit test that 207 sheriff annotations name.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ...". No failure rate, history, or task list for geckoview-junit.
- Workaround: Treeherder `api/failuresbybug` for the history, and `api/jobs/?push_id=` per push to count jobs. That is about 170 API calls to learn "0 failures in 88 jobs since the fix".
- Question it could have answered: "how often does this junit test fail per config, and has it failed since revision X?"

## `task <id>` without a run number picks run 0 even when run 0 is an infra exception
- Command: `fx-tests task CrreROmoRyCHMJXYNLbjKQ --profiles`
- Expected: the run that has artifacts (run 1; run 0 was `exception / worker-shutdown`).
- Got: "has no profile_resource-usage.json ... Taskcluster expires task artifacts after about a month, so this is permanent". The artifact exists on run 1, and the task is 1 day old. The message is wrong on both counts.
- Workaround: `fx-tests task CrreROmoRyCHMJXYNLbjKQ.1`. `fx-tests intermittent --bug` prints `.1` in its occurrence list, but the bare task id is the one people paste.

## `task <id>.1 --profiles` says the profile records no tests, but it has 1457 `test` markers
- Command: `fx-tests task CrreROmoRyCHMJXYNLbjKQ.1 --profiles`
- Expected: the junit tests with their status. The resource-usage profile has `test` markers such as `FAIL — org.mozilla.geckoview.test.MediaSessionTest#audioSessionTypeMediaElementIsPlayback`.
- Got: "0 tests, 0 executions, 0 failing ... This profile records no tests at all", plus a warning about "134 failing markers ... named no test path".
- Workaround: loaded the profile with profiler-cli and searched the `test` markers by name.
