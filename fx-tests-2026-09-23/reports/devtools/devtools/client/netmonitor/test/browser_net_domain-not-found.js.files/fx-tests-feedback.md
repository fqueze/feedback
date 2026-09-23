## `fx-tests test <path> --bugs` prints no bug section at all

Command: `fx-tests test devtools/client/netmonitor/test/browser_net_domain-not-found.js --bugs`

Expected: the bugs naming the test, or an explicit "no bug found" line. Bug 1815641 ("Intermittent devtools/client/netmonitor/test/browser_net_domain-not-found.js | single tracking bug", RESOLVED INCOMPLETE) exists.
Got: the same output as without `--bugs`, no bug section and no "none" line, so "no bug" and "flag ignored" look identical.
Workaround: `curl 'https://bugzilla.mozilla.org/rest/bug?summary=browser_net_domain-not-found'`.

