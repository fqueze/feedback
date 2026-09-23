## Question: is there a bug for this test?

- Command: `fx-tests test browser/base/content/test/general/browser_refreshBlocker.js --bugs` (and the same for the new `browser-general` path)
- Expected: bug 1775729 ("Intermittent browser/base/content/test/general/browser_refreshBlocker.js | single tracking bug", REOPENED), or an explicit "no bug found" line.
- Got: the ordinary `fx-tests test` output with no bug section at all, so "no bug" and "flag ignored" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_refreshBlocker.js`.

## Question: how many jobs were starred on the bug?

- Command: `fx-tests intermittent --bug 1775729`
- Got: "no sheriff annotations ... on trunk between 2026-09-16 and 2026-09-22", although the test failed 3 times on autoland in that window. `--since 21 --tree all` finds 5 annotations, all on esr115 and beta.
- Not necessarily wrong (the autoland failures were retried green and never starred), but the message could say that the test's failures in the window were all retried green, since that is what the reader is about to wonder.
