# fx-tests feedback (browser_dbg-blackbox-all.js)

## Question: "which bug tracks this test?"
- Command: `fx-tests test devtools/client/debugger/test/mochitest/browser_dbg-blackbox-all.js --bugs`
- Expected: bug 1767715 ("Intermittent devtools/client/debugger/test/mochitest/browser_dbg-blackbox-all.js | single tracking bug", REOPENED), which names the test in its summary.
- Got: the normal `fx-tests test` output with no bugs section, and no "no bug found" line either.
- Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?quicksearch=browser_dbg-blackbox-all.js'`.
