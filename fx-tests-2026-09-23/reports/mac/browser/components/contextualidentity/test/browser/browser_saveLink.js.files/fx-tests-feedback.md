## `--bugs` prints nothing about bugs, not even "none"

- Command: `fx-tests test browser/components/contextualidentity/test/browser/browser_saveLink.js --bugs`
- Expected: the bugs naming the test, or a line saying none were annotated in the window. Bugzilla has bug 1420327 ("Intermittent browser/components/contextualidentity/test/browser/browser_saveLink.js | Test timed out -", NEW), with no sheriff annotation in the last 30 days (the failing jobs went green on the harness retry).
- Got: output byte-identical to the run without `--bugs` (diffed), exit 0, nothing on stderr. Reads as "the flag did nothing".
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?quicksearch=<test file name>"`.
