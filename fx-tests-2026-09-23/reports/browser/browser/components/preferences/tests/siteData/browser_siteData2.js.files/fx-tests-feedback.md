## `fx-tests test` names a TEST-KNOWN-FAIL as the failure mode

- Command: `fx-tests test browser/components/preferences/tests/siteData/browser_siteData2.js`
- Expected: Issues to list the TEST-UNEXPECTED-FAIL, `This test exceeded the timeout threshold ... Test ran for 49s, limit was 45s`.
- Got: `1. 284x FAIL changed preference: identity.fxaccounts.account.device.name`. In the profile (task JEJOVIMST3mCthTjspIvDQ) that message is `TEST-KNOWN-FAIL` (browser-test.js records it with `todo: !gConfig.comparePrefs`); the real failure is logged right after it. `fx-tests task <id> --messages` shows both, still with the pref first.
- Cost: the headline failure mode is wrong for all 284 runs; it would have sent the diagnosis after a pref leak.
- Workaround: read the TEST-UNEXPECTED-FAIL from the per-test profile's Test markers.
