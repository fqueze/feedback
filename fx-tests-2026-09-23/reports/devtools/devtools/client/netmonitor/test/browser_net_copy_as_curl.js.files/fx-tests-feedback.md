## `fx-tests test <path> --bugs` prints no bug section at all
- Command: `COLUMNS=250 fx-tests test devtools/client/netmonitor/test/browser_net_copy_as_curl.js --bugs`
- Expected: a "Bugs" section, or an explicit "no bug names this test".
- Got: the same output as without `--bugs`; no line about bugs. Bugzilla has a (closed) single-tracking bug 1767639 for this test.
- Workaround: `curl https://bugzilla.mozilla.org/rest/bug?summary=browser_net_copy_as_curl`.
