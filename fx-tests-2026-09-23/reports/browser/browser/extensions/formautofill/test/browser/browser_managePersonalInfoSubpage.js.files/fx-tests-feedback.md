## `Issues (first failure per run)` names a TEST-KNOWN-FAIL line instead of the failure

- Command: `fx-tests test browser/extensions/formautofill/test/browser/browser_managePersonalInfoSubpage.js`
- Expected: the failure mode to be `This test exceeded the timeout threshold ... limit was 45s`, the only TEST-UNEXPECTED-FAIL of these runs.
- Got: `43x FAIL changed preference: browser.crashReports.cleanupCheck.lastDate` and `2x FAIL changed preference: identity.fxaccounts.account.device.name`. In the per-test profile these are `TEST-KNOWN-FAIL` (the harness's `checkPreferencesAfterTest` records them as `todo` when `--compare-preferences` is not passed, which is the case in CI). `fx-tests task <id> --json` also puts the known-fail line first in `messages`. `fx-tests failures --message cleanupCheck.lastDate` then attributes 126 "failures" to 5 tests for a pref that never fails anything.
- Workaround: `fx-tests task <id> --messages` for every task, then the profile's Test markers to see which line was UNEXPECTED.
- Cost: I spent the first part of the investigation reading the CrashFileCleaner code and its 5-minute timer, a lead that went nowhere.

## Question: how long did each execution of this test take within one job (first run vs harness retry)?

- Command: `fx-tests task Y9w02fRyR9qdnDxAkYA-WQ.0 --json`
- Expected: a duration for each execution in `failures[]` (first run 57.6 s FAIL, retry 19.1 s PASS), since "passed on rerun" does not say whether the retry was much faster.
- Got: no durations. I had to load the resource-usage profile and read the `test` markers.
- What the output could show: `executions: [{status, durationMs, retry: bool}]` for each failing test.
