## `fx-tests test` names a TEST-KNOWN-FAIL as the failure mode

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_manage_tabs_tool.js`
- Expected: Issues #1 to be the TEST-UNEXPECTED-FAIL that made the runs fail: `This test exceeded the timeout threshold ... Test ran for Ns, limit was 90s`.
- Got: `1. 1110x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime`. In the per-test profile (task VaPJ4I47R1izPV2twLUi-A) that line is TEST-KNOWN-FAIL (browser-test.js reports it with `pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`, and CI does not pass `--compare-preferences`). `fx-tests task <id> --messages` lists it too, next to the real failure, without saying which is which. `--issue 1` then selects on a message that is not a failure.
- Workaround: read the test log in the profile (`thread markers --category Test --search <test> --list`), where the status column shows TEST-KNOWN-FAIL vs TEST-UNEXPECTED-FAIL.
- Cost: I spent the start of the investigation reading SpecialPowers' pref comparison and the product code that sets the pref, chasing a failure mode that does not exist.
