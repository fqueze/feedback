# fx-tests feedback

## `test` lists a TEST-KNOWN-FAIL as the failure of each run

- Command: `fx-tests test browser/base/content/test/protectionsUI/browser_protectionsUI_open_preferences.js`
  (and the same for the new path `browser/base/content/test/browser-protectionsUI/...`).
- Expected: under "Issues (first failure per run)", the message that made the run fail.
- Got: `16x FAIL changed preference: browser.protections_panel.infoMessage.seen`. In the profile
  (`LP-t-la0Rkerhckdn6pRzQ`), that message is a `TEST-KNOWN-FAIL` (browser-test.js reports it
  with `pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`, and CI does not pass
  `--compare-preferences`). The actual `TEST-UNEXPECTED-FAIL` is the third message,
  `This test exceeded the timeout threshold ... Test ran for 47s, limit was 45s`, only visible
  with `fx-tests task <id> --messages`, and there it is listed with the known-fails as if all
  three were failures.
- Workaround: `fx-tests task <id> --messages`, then the profile's Test markers to see which
  one is TEST-UNEXPECTED-FAIL.
- Cost: I started diagnosing a pref leak that is not the failure.

## Question: "was the machine busy during this test?" from the resource-usage profile

- Command: `profiler-cli thread markers --search "name:CPU Use" --list --limit 0 --json` in a
  zoom on the test's `test` marker, then a Python script averaging `data.cpuPercent` per 5 s.
- `fx-tests task <id> --profiles` could print, per failing test, the mean/max machine CPU over
  the test's own interval from the resource-usage profile.
