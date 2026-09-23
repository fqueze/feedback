## `--bugs` finds nothing when the bug names the test's old path (browser_override.js)

- Command: `fx-tests test browser/components/urlbar/tests/browser-tabs/browser_override.js --bugs`
- Expected: bug 2036623, "Intermittent browser/components/urlbar/tests/browser-switchTab/browser_override.js | single tracking bug". Sheriffs annotate failures of the *current* path on it: `fx-tests intermittent --bug 2036623` lists "Tests named: browser/components/urlbar/tests/browser-tabs/browser_override.js".
- Got: the normal `test` output with no bugs section and nothing on stderr. It did not say that it searched and found none.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_override.js"`.
- Question behind it: "which bugs are sheriffs starring this test's failures on?". The annotations already link the test path to the bug, whatever the bug's summary says. Also, an empty result should print "no bugs found" rather than nothing at all.

