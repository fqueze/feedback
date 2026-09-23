## `fx-tests test` / `fx-tests task` do not cover geckoview-junit (same as DynamicToolbarTest.kt.bug2033703's feedback)

- Commands: `fx-tests test mobile/android/geckoview/src/androidTest/java/org/mozilla/geckoview/test/InteractiveWidgetTest.kt`; `fx-tests task a4ZWLWR8SAynT3hukCAJxA --profiles`
- Expected: runs and failures per config; for the task, the job's tests, with `InteractiveWidgetTest#bug1994311` as FAIL.
- Got: "No test path in the xpcshell and mochitest 21-day data contains ..."; and for the task, `0 tests, 0 executions, 0 failing`, "This profile records no tests at all", plus "139 failing markers ... named no test path". Yet the resource-usage profile has a `test` marker `FAIL — org.mozilla.geckoview.test.InteractiveWidgetTest#bug1994311`.
- Workaround: `fx-tests intermittent --bug 1998616 --limit 0` for the task ids, Treeherder `api/failurecount/?bug=` for the rate, `profiler-cli thread markers --search` for the marker.

## Question: "passing runs of this junit test on the same pushes, for comparison"

- There was no fx-tests command for it. I used Treeherder `api/project/autoland/push/?revision=` + `api/jobs/?push_id=` to list the geckoview-junit jobs on the failing pushes, then downloaded 10 `logcat-emulator-5554.log` and grepped each for `TestRunner: finished: bug1994311`.
- Could have shown: for a test, the task ids of the jobs where it passed, next to the failing ones (`--task-ids --passing`), for any harness.
- The `failurecount` history also stops at about 2026-05-25 (Treeherder retention), which looked like a step change until I checked it against another bug. A note on the window's start would have saved that.
