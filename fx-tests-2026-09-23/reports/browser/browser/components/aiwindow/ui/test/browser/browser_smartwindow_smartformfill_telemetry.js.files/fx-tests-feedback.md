## `fx-tests test` labels a failure mode with a TEST-KNOWN-FAIL message

- Command: `fx-tests test browser/components/aiwindow/ui/test/browser/browser_smartwindow_smartformfill_telemetry.js`
- Expected: issue 3 labelled with the run's first unexpected failure, `This test exceeded the timeout threshold ... limit was 90s`.
- Got: `17x FAIL changed preference: browser.smartwindow.lastSmartWindowUsageTime`. In the profile that message is `TEST-KNOWN-FAIL` (a todo: `comparePrefs` is off in CI), logged just before the real `TEST-UNEXPECTED-FAIL`. The same label covers 2,273 failures in 18 aiwindow tests in `fx-tests failures`, so it hides whatever each of them actually failed on.
- Workaround: `fx-tests task <id> --messages` for each task, then read the profile to see which message is unexpected.
- Would have helped: skip known-fail and todo results when choosing the "first failure" message.
