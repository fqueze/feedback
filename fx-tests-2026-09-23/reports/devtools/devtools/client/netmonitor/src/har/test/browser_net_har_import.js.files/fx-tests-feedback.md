## `--bugs` with no bug found prints nothing

- Command: `fx-tests test devtools/client/netmonitor/src/har/test/browser_net_har_import.js --bugs`
- Expected: a "Bugs" section, either listing bug 1767273 ("Intermittent devtools/client/netmonitor/src/har/test/browser_net_har_import.js | Test timed out -", RESOLVED FIXED by a skip patch) or saying "no open bug names this test".
- Got: the normal `test` output with no Bugs section at all, so "no bug" and "flag ignored" look the same.
- Workaround: Bugzilla REST lookup of the bug named in the manifest's skip-if comment.
