## `fx-tests test` Issues names a todo as the failure mode

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_manage_tabs_tool.js`
- Expected: issue 1 to be the message that made the runs fail: `This test exceeded the timeout threshold ... limit was 90s`.
- Got: `1110x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime`. browser-test.js
  `checkPreferencesAfterTest` records that one with `pass: !gConfig.comparePrefs, todo: !gConfig.comparePrefs`,
  so it is a todo in CI. It is logged before the timeout-threshold failure, so "first failure per run" picks it.
  The real failure mode appears nowhere in the Issues list.
- Workaround: `fx-tests task <id> --messages` on one failing job shows all the messages, with the real failure.

## Question: "which failure message was annotated on each occurrence of this bug"

- Command: `fx-tests intermittent --bug 2057483 --since 60 --limit 0`
- The Occurrences table has task ids but not the line(s) each one was annotated for, and the bug mixes
  timeouts, ShutdownLeaks and leakcheck. I needed `--json` and `occurrenceRows[].lines` to tell which
  occurrence was which failure. A message column (or `--messages`) on the Occurrences table would answer it.
