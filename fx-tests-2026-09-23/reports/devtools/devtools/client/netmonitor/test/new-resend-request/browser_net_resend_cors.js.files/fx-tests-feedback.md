## Question: which bugs name this test? (report browser_net_resend_cors.js)

- Command: `fx-tests test devtools/client/netmonitor/test/new-resend-request/browser_net_resend_cors.js --bugs`
- Expected: the bugs naming the test, or an explicit "no bug found" line.
- Got: the normal `test` output with no bugs section and no line saying none was found. A Bugzilla summary search finds bug 1767650, "Intermittent devtools/client/netmonitor/test/browser_net_resend_cors.js | single tracking bug", resolved INCOMPLETE by BugBot. It names the test's old path, without `new-resend-request/`, and none of the 4 trunk failures in the window was starred on it.
- Workaround: `curl "https://bugzilla.mozilla.org/rest/bug?summary=browser_net_resend_cors"`.
- What would have answered it: match bug summaries on the test file name as well as the full path (tests move), and print "no annotated bug in the window" when nothing matches.
