## Question: "which bug tracks this test?"

- Command: `fx-tests test devtools/client/netmonitor/src/har/test/browser_net_har_post_data.js --bugs`
- Expected: bug 1862292 ("Intermittent devtools/client/netmonitor/src/har/test/browser_net_har_post_data.js | single tracking bug", RESOLVED INCOMPLETE on 2026-08-31), or at least a line saying no annotated bug was found and why (closed bugs excluded, no annotations in the window).
- Got: the normal `test` output with no bugs section at all, so "no bug" and "flag ignored" look the same.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_net_har_post_data`.
