## Issues block names a known-fail result as the failure (browser_policy_set_homepage.js)

- Command: `fx-tests test browser/components/enterprisepolicies/tests/browser/browser_policy_set_homepage.js`
- Expected: the Issues row to be the run's TEST-UNEXPECTED-FAIL, `This test exceeded the timeout threshold ... limit was 120s`.
- Got: `15x FAIL changed preference: identity.fxaccounts.account.device.name`. That is a todo result from `checkPreferencesAfterTest` (`pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`), TEST-KNOWN-FAIL in the per-test profile. It is only logged because the buffered output of a failing test is flushed, and it comes before the real failure, so "first failure per run" picks it.
- Workaround: `fx-tests task <taskId> --messages --full-messages` lists the timeout-threshold message.
- Cost: this points a diagnosis at the FxA device-name pref, which has nothing to do with the failure.

## "Did it fail anywhere after the fix?" misses test-verify

- Command: `fx-tests test <path> --history` / `--coverage`
- Question: did the test still fail anywhere after bug 2068142's fix landed?
- Got: 0 failures since 2026-09-17, and no test-verify config in `--coverage`. The test-verify job on the fix push itself (CjL8DWLcT36hoSVroCglmg, macOS opt) failed. Only `fx-tests intermittent --bug 2068142` showed it.
- Could have shown: test-verify runs in `fx-tests test`, or a line saying TV jobs are not in the data.
