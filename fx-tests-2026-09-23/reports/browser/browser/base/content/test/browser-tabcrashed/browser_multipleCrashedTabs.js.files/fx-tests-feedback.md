## Question: which bugs name this test?

- Command: `fx-tests test browser/base/content/test/browser-tabcrashed/browser_multipleCrashedTabs.js --bugs`
  (and the same for the old `tabcrashed/` path).
- Expected: the bugs naming the test, or an explicit "no bugs found".
- Got: the usual summary with no bug section and no line saying none were found.
- Workaround: Bugzilla REST `bug?summary=browser_multipleCrashedTabs` found bug 1724821 (this exact
  failure message) and bug 1838196 (single tracking bug), both RESOLVED INCOMPLETE. If `--bugs`
  only lists open bugs, saying so, and listing closed ones with the same message, would have
  answered it.
