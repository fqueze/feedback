## `--bugs` finds no bug for a test whose directory was renamed

- Command: `fx-tests test browser/components/urlbar/tests/browser-tabs/browser_override.js --bugs`
- Expected: bug 2036623 ("Intermittent browser/components/urlbar/tests/browser-switchTab/browser_override.js | single tracking bug"), which sheriffs still star this test's failures on (`fx-tests intermittent --bug 2036623` lists this exact path under "Tests named").
- Got: the normal `test` output with no bug section and no "no bug found" line, so it is not even clear the flag did anything.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_override.js"`.
- Suggestion: match on the file name as well as the full path (or use the sheriff annotations, which already map this path to the bug), and print "no bug found" when there is none.
