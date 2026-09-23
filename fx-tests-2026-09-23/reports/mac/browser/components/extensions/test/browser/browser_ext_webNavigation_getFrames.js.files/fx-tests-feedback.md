## `fx-tests test --bugs` prints nothing when no bug is found

- Command: `fx-tests test browser/components/extensions/test/browser/browser_ext_webNavigation_getFrames.js --bugs`
- Expected: a line such as "No sheriff-annotated bugs name this test in the window".
- Got: the same output as without `--bugs`, so it is unclear whether the lookup ran, failed, or found nothing.
- Workaround: `--json` shows `annotatedBugs: []`. Bugzilla's summary search then found bug 1717520 (2021-2022, same "No frame found" failure mode, closed as a duplicate of a tracking bug), which is outside the window.
