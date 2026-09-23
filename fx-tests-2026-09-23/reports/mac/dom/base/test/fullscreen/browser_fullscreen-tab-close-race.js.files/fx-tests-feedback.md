## `fx-tests test <path> --bugs` prints nothing about bugs

- Question: which bug names this test?
- Command: `fx-tests test dom/base/test/fullscreen/browser_fullscreen-tab-close-race.js --bugs`
- Expected: a Bugs section, even if it says "none", or bug 1750901 ("Intermittent test failures on browser_fullscreen-tab-close-race.js on non-fission mode", NEW), which Bugzilla quicksearch finds by the file name.
- Got: output identical to `fx-tests test <path>` without the flag; no line about bugs at all, so "no bug" and "flag ignored" look the same.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_fullscreen-tab-close-race'`.
