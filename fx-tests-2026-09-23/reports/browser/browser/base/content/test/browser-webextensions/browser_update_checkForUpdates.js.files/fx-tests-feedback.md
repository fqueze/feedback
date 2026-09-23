## A `todo` is reported as the failure

- Command: `fx-tests test browser/base/content/test/webextensions/browser_update_checkForUpdates.js` (and `fx-tests task <id>`, `--messages`)
- Expected: Issues line 1 = `Shutdown - leaked window until shutdown [url = about:addons]`, the message that made the run fail.
- Got: `24x FAIL handleEvent() was unable to perform a11y checks on hidden node: id: , tagName: BUTTON, className:`. That message comes from `AccessibilityUtils`' `a11yWarn`, i.e. `SimpleTest.todo(false, ...)`: TEST-FAIL with expected FAIL. The job's own summary counts it under `Todo: 6`, and it only appears in the log because the harness dumps buffered messages after the leak. In `task --messages` it is listed before the real failure too.
- Workaround: read `fx-tests task <id> --messages` and the live log; the leak lines were there.
- Suggestion: drop test_status entries whose status equals expected (todo / known fail) from "first failure per run" and from `--messages`, or mark them `todo`.

## Tasks with no per-test profile say only "No failing test named a per-test profile"

- Command: `fx-tests task ILyo9isOSdSpQ30LnTbbdw --profiles`
- Got: "No failing test named a per-test profile in this job." for all 25 failing tasks of this test.
- Would help: saying why (a shutdown-time leak failure is detected after the test finished, so no per-test profile is captured), so one does not go looking for it in artifacts.
- Likely source: in the resource-usage profile, the todo is a `TestStatus` marker named `FAIL` whose payload has no expected status (see `marker info` on it in ILyo9isOSdSpQ30LnTbbdw), so it is indistinguishable from a real failure there. If fx-tests reads these markers, the fix may belong in the marker (add `expected`) as well.
